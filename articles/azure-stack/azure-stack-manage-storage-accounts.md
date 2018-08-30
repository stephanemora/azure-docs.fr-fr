---
title: Gérer les comptes de stockage Azure Stack | Microsoft Docs
description: Découvrez comment rechercher, gérer, restaurer et récupérer des comptes de stockage Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 8914391a586bb508192200beaba7f591649a1e99
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2018
ms.locfileid: "43050385"
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Gérer les comptes de stockage dans Azure Stack
Découvrez comment gérer les comptes de stockage dans Azure Stack pour rechercher, restaurer et récupérer de la capacité de stockage en fonction des besoins de l’entreprise.

## <a name="find"></a>Rechercher un compte de stockage
La liste des comptes de stockage de la région peut être affichée dans Azure Stack comme suit :

1. Dans un navigateur Internet, accédez à https://adminportal.local.azurestack.external.
2. Connectez-vous au portail d’administration d’Azure Stack comme opérateur cloud (en utilisant les informations d’identification que vous avez fournies lors du déploiement)
3. Dans le tableau de bord par défaut, recherchez la liste **Gestion des régions** et sélectionnez la région à explorer, par exemple **(local**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Sélectionnez **Stockage** dans la liste **Fournisseurs de ressources**.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Ensuite, dans le volet de l’administrateur du fournisseur de ressources de stockage, faites défiler jusqu’à l’onglet **Comptes de stockage** et sélectionnez-le.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   La page résultante est la liste des comptes de stockage dans cette région.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Par défaut, les 10 premiers comptes sont affichés. Vous pouvez choisir d’en afficher plus en cliquant sur le lien **Charger plus** en bas de la liste.

Ou

Si vous êtes intéressé par un compte de stockage particulier, vous pouvez **filtrer et extraire les comptes appropriés** uniquement.


**Pour filtrer les comptes :**

1. Sélectionnez **Filtrer** en haut du volet.
2. Dans le volet Filtrer, vous pouvez spécifier un **nom de compte**, un **ID d’abonnement ou un **statut** pour affiner la liste des comptes de stockage à afficher. Utilisez-les pour filtrer selon vos besoins.
3. Sélectionnez **Update**. La liste est normalement actualisée en conséquence.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Pour réinitialiser le filtre, sélectionnez **Filtrer**, effacez les sélections, puis mettez à jour la liste.

La zone de texte de recherche (en haut du volet de la liste de comptes de stockage) vous permet de mettre en surbrillance le texte sélectionné dans la liste des comptes. Vous pouvez l’utiliser lorsque le nom complet ou l’ID n’est pas facilement disponible.

Vous pouvez utiliser ici du texte libre pour rechercher le compte qui vous intéresse.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Accéder aux détails du compte
Une fois que vous avez trouvé les comptes qui vous intéressent, vous pouvez sélectionner un compte particulier pour afficher certains détails. Un nouveau volet s’ouvre avec les informations détaillées du compte, comme le type du compte, l’heure de création, l’emplacement, etc.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Récupérer un compte supprimé
Il peut être parfois nécessaire de récupérer un compte supprimé.

Dans Azure Stack, il existe un moyen simple de le faire :

1. Accédez à la liste de comptes de stockage. Pour plus d’informations, consultez [Rechercher un compte de stockage](#find) dans cette rubrique.
2. Localisez ce compte particulier dans la liste. Il peut être nécessaire de filtrer.
3. Vérifiez l’*état* du compte. Il doit être **Supprimé**.
4. Sélectionnez le compte pour ouvrir le volet des détails du compte.
5. En haut de ce volet, recherchez le bouton **Récupérer** et sélectionnez-le.
6. Sélectionnez **Oui** pour confirmer.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. La récupération est maintenant *en cours... Attendez* un message indiquant que la récupération est effective.
   Vous pouvez aussi sélectionner l’icône « cloche » en haut du portail pour voir des indications sur la progression.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Une fois que le compte récupéré est synchronisé, il peut à nouveau être utilisé.

### <a name="some-gotchas"></a>Quelques astuces
* Votre compte supprimé affiche un état **hors conservation**.
  
  « Hors rétention » signifie que le compte supprimé a dépassé la période de conservation et qu’il n’est peut-être pas récupérable.
* Votre compte supprimé n’apparaît pas dans la liste des comptes.
  
  Il se peut que votre compte ne s’affiche pas dans la liste des comptes lorsque les comptes supprimés ont déjà été effacés. Dans ce cas, il ne peut pas être récupéré. Consultez [Récupérer de la capacité](#reclaim) dans cette rubrique.

## <a name="set-the-retention-period"></a>Définir la période de conservation
Le paramètre de période de conservation permet à un opérateur cloud de spécifier une période de temps en jours (entre 0 et 9 999 jours) pendant laquelle un compte supprimé peut être récupéré. La période de rétention par défaut est définie sur 0 jour. La valeur « 0 » pour ce paramètre signifie qu’un compte supprimé est immédiatement hors conservation et est marqué pour faire l’objet d’un nettoyage périodique de la mémoire.

**Pour changer la période de conservation :**

1. Dans un navigateur Internet, accédez à https://adminportal.local.azurestack.external.
2. Connectez-vous au portail d’administration d’Azure Stack comme opérateur cloud (en utilisant les informations d’identification que vous avez fournies lors du déploiement)
3. Dans le tableau de bord par défaut, recherchez la liste **Gestion des régions** et sélectionnez la région à explorer, par exemple **(local**).
4. Sélectionnez **Stockage** dans la liste **Fournisseurs de ressources**.
5. Sélectionnez **Paramètres** en haut pour ouvrir le volet des paramètres.
6. Sélectionnez **Configuration**, puis modifiez la valeur de la période de conservation.

   Définissez le nombre de jours et enregistrez-le.
   
   Cette valeur est prise en compte immédiatement et est appliquée à toute votre région.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Récupérer de la capacité
Un des effets secondaires de la période de conservation est qu’un compte supprimé continue à consommer de la capacité jusqu’à ce qu’il sorte de cette période de conservation. En tant qu’opérateur cloud, vous pouvez avoir besoin d’un moyen de récupérer l’espace du compte supprimé, même si la période de conservation n’a pas encore expiré.

Vous pouvez récupérer de la capacité en utilisant le portail ou PowerShell.

**Pour récupérer de la capacité en utilisant le portail :**
1. Accédez au volet des comptes de stockage. Consultez [Rechercher un compte de stockage](#find).
2. Sélectionnez **Récupérer de l’espace** en haut du volet.
3. Lisez le message, puis sélectionnez **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Attendez la notification de la réussite de l’opération. Utilisez l’icône de cloche sur le portail.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Actualisez la page Comptes de stockage. Les comptes supprimés ne figurent plus dans la liste, car ils ont été supprimés définitivement.

Vous pouvez aussi utiliser PowerShell pour remplacer explicitement la période de rétention et récupérer immédiatement de la capacité.

**Pour récupérer de la capacité en utilisant PowerShell :**   

1. Vérifiez qu’Azure PowerShell est installé et configuré. Dans le cas contraire, suivez ces instructions : 
   * Pour installer la dernière version d’Azure PowerShell et l’associer à votre abonnement Azure, consultez [Installer et configurer Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Pour plus d’informations sur les applets de commande Azure Resource Manager, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767).
2. Exécutez les applets de commande suivantes :

> [!NOTE]
> Si vous exécutez ces applets de commande, vous supprimez définitivement le compte et son contenu. Il n’est pas récupérable. Utilisez cette option avec précaution.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
````

Pour plus d’informations, consultez la [documentation Azure Stack PowerShell](https://docs.microsoft.com/powershell/module/azurerm.azurestackstorage).
 

## <a name="next-steps"></a>Étapes suivantes

 - Pour plus d’informations sur la gestion des autorisations, consultez [Gérer le contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md).
 - Pour plus d’informations sur la gestion de la capacité de stockage pour Azure Stack, consultez [Gérer la capacité de stockage pour Azure Stack](azure-stack-manage-storage-shares.md).