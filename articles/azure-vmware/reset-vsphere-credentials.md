---
title: Réinitialiser les informations d’identification vSphere pour Azure VMware Solution
description: Découvrez comment réinitialiser les informations d’identification vSphere pour votre cloud privé Azure VMware Solution et vérifier que le connecteur HCX dispose des informations d’identification vSphere les plus récentes.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e5a15caa98a46b0ae75b68ee7b568dabdbf1956c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103602881"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Réinitialiser les informations d’identification vSphere pour Azure VMware Solution

Dans cet article, nous allons examiner les étapes de réinitialisation des informations d’identification vSphere pour votre cloud privé Azure VMware Solution. Vous pouvez ainsi vous assurer que le connecteur HCX dispose des informations d’identification vSphere les plus récentes.

## <a name="reset-your-vsphere-credentials"></a>Réinitialiser vos informations d’identification vSphere

 Tout d’abord, réinitialisez vos informations d’identification vSphere. Vos informations d’identification d’administrateur NSX-T et CloudAdmin vCenter n’expirent pas. Toutefois, vous pouvez suivre ces étapes pour générer de nouveaux mots de passe pour ces comptes.

> [!NOTE]
> Si vous utilisez vos informations d’identification CloudAdmin pour des services connectés tels que HCX, vCenter Orchestrator, vCloud Director ou vRealize, vos connexions cessent de fonctionner une fois que vous avez mis à jour votre mot de passe.  Ces services doivent être arrêtés avant le lancement de la rotation du mot de passe.  Sinon, vous risquez d’obtenir des verrous temporaires sur vos comptes d’administrateur NSX-T et CloudAdmin vCenter, car ces services appelleront en permanence à l’aide de vos anciennes informations d’identification.  Pour plus d’informations sur la configuration de comptes distincts pour les services connectés, consultez la page sur les [concepts d’accès et d’identité](https://docs.microsoft.com/azure/azure-vmware/concepts-identity).

1. Dans votre portail Azure VMware Solution, ouvrez une ligne de commande.

2. Exécutez la commande suivante pour mettre à jour votre mot de passe CloudAdmin vCenter.  Vous devez remplacer {SubscriptionID}, {ResourceGroup} et {PrivateCloudName} par les valeurs réelles du cloud privé auquel appartient le compte CloudAdmin.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Exécutez la commande suivante pour mettre à jour votre mot de passe d’administrateur NSX-T. Vous devez remplacer {SubscriptionID}, {ResourceGroup} et {PrivateCloudName} par les valeurs réelles du cloud privé auquel appartient le compte d’administrateur NSX-T.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vsphere-credentials"></a>Vérifier que le connecteur HCX dispose des informations d’identification vSphere les plus récentes

Maintenant que vous avez réinitialisé vos informations d’identification, procédez comme suit pour vous assurer que le connecteur HCX dispose de vos informations d’identification mises à jour.

1. Une fois que vous avez modifié votre mot de passe, accédez à l’interface web du connecteur HCX local à l’aide de https://{IP de l’appliance de connecteur HCX}:443. Veillez à utiliser le port 443. Connectez-vous à l’aide de vos nouvelles informations d’identification.

2. Dans le tableau de bord VMware HCX, sélectionnez **Site Pairing (Jumelage de sites)** .
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Capture d’écran du tableau de bord VMware HCX avec l’option Site Pairing mise en surbrillance.":::
 
3. Sélectionnez la connexion correcte à AVS (s’il en existe plusieurs) et sélectionnez **Modifier la connexion**.
 
4. Fournissez les nouvelles informations d’identification vSphere et sélectionnez **Modifier**, ce qui permet d’enregistrer les informations d’identification. L’enregistrement doit s’afficher correctement.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez réinitialiser des informations d’identification vSphere pour Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants :

- [Configurer des composants réseau NSX dans Azure VMware Solution](configure-nsx-network-components-azure-portal.md).
- [Gestion du cycle de vie des machines virtuelles Azure VMware Solution](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Déploiement d’une récupération d’urgence de machines virtuelles à l’aide d’Azure VMware Solution](disaster-recovery-for-virtual-machines.md).
