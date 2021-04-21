---
title: Démarrer une machine virtuelle lors de la connexion – Azure
description: Comment configurer la fonctionnalité de démarrage de machine virtuelle lors de la connexion.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: af95cf5d3e4112c717d653062f186797d48fb515
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389806"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Démarrer une machine virtuelle lors de la connexion (préversion)

> [!IMPORTANT]
> La fonctionnalité de démarrage de machine virtuelle lors de la connexion est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La fonctionnalité de démarrage de machine virtuelle lors de la connexion (préversion) vous aide à réduire les coûts en vous permettant de libérer vos machines virtuelles quand vous ne les utilisez pas. Quand vous avez besoin d’utiliser à nouveau une machine virtuelle, il vous suffit de la réactiver.

>[!NOTE]
>Windows Virtual Desktop (classique) ne prend pas en charge cette fonctionnalité.

## <a name="requirements-and-limitations"></a>Conditions requises et limitations :

Vous ne pouvez activer la fonctionnalité de démarrage de machine virtuelle lors de la connexion que pour des pools d’hôtes personnels. Pour en savoir plus sur les pools d’hôtes personnels, consultez [Environnement Windows Virtual Desktop](environment-setup.md#host-pools).

Les clients Bureau à distance prenant en charge la fonctionnalité de démarrage de machine virtuelle lors de la connexion sont les suivants :

- [client web](connect-web.md) ;
- [client Windows (version 1.2748 ou ultérieure)](connect-windows-7-10.md).

Vous pouvez trouver des annonces sur les mises à jour et la prise en charge de client sur le [Forum Tech Community](https://aka.ms/wvdtc).

Le cloud Azure Government ne prend actuellement pas en charge Démarrer la machine virtuelle à la connexion.

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Créer un rôle personnalisé pour démarrer une machine virtuelle lors de la connexion

Avant de pouvoir configurer la fonctionnalité de démarrage de machine virtuelle lors de la connexion, vous devez attribuer à votre machine virtuelle un rôle RBAC (contrôle d’accès en fonction du rôle) personnalisé. Ce rôle permet à Windows Virtual Desktop de gérer les machines virtuelles incluses dans votre abonnement. Vous pouvez également utiliser ce rôle pour activer des machines virtuelles, vérifier leur état et rapporter des informations de diagnostic. Pour en savoir plus sur ce que fait chaque rôle, consultez [Rôles personnalisés Azure](../role-based-access-control/custom-roles.md).

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Pour utiliser le portail Azure afin d’attribuer un rôle personnalisé pour démarrer un machine virtuelle lors de la connexion :

1. Ouvrez le portail Azure et accédez à **Abonnements**.

2. Accédez à **Contrôle d’accès (IAM)** , puis sélectionnez **Ajouter un rôle personnalisé**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du menu déroulant accessible via le bouton Ajouter dans Contrôle d’accès (IAM). L’option « Ajouter un rôle personnalisé » est mise en évidence en rouge.](media/add-custom-role.png)

3. Ensuite, nommez le rôle personnalisé et ajoutez une description. Nous vous suggérons de le nommer « Démarrer une machine virtuelle lors de la connexion ».

4. Sous l’onglet **Autorisations**, ajoutez les autorisations suivantes à l’abonnement auquel vous attribuez le rôle : 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read

5. Lorsque vous avez terminé, sélectionnez **OK**.

Ensuite, vous devez attribuer le rôle pour accorder l’accès à Windows Virtual Desktop.

Pour attribuer le rôle personnalisé :

1. Sous l’**onglet Contrôle d’accès (IAM)** , sélectionnez **Ajouter des attributions de rôle**.

2. Sélectionnez le site que vous venez de créer.

3. Dans la barre de recherche, entrez et sélectionnez **Windows Virtual Desktop**.

      >[!NOTE]
      >Il se peut que deux applications s’affichent si vous avez déployé Windows Virtual Desktop (classique). Attribuez le rôle aux deux applications que vous voyez.
      >
      > [!div class="mx-imgBorder"]
      > ![Capture d’écran de l’onglet Contrôle d’accès (IAM). Dans la barre de recherche, Windows Virtual Desktop et Windows Virtual Desktop (classique) sont mis en évidence en rouge.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Créer un rôle personnalisé avec un modèle JSON

Si vous utilisez un fichier JSON pour créer le rôle personnalisé, l’exemple suivant présente un modèle de base que vous pouvez utiliser. Veillez à remplacer la valeur d’ID d’abonnement par l’ID d’abonnement auquel vous voulez attribuer le rôle.

```json
{
    "properties": {
        "roleName": "start VM on connect",
        "description": "Friendly description.",
        "assignableScopes": [
            "/subscriptions/<SubscriptionID>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/virtualMachines/start/action",
                    "Microsoft.Compute/virtualMachines/read"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="configure-the-start-vm-on-connect-feature"></a>Configurer la fonctionnalité de démarrage de machine virtuelle lors de la connexion

À présent que vous avez attribué le rôle à votre abonnement, il est temps de configurer la fonctionnalité de démarrage de machine virtuelle lors de la connexion.

### <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement 

La fonctionnalité de démarrage de machine virtuelle lors de la connexion est un paramètre de pool d’hôtes. Si vous souhaitez qu’un groupe d’utilisateurs spécifique utilise cette fonctionnalité, veillez à n’attribuer le rôle requis qu’aux utilisateurs que vous souhaitez ajouter.

>[!IMPORTANT]
> Vous ne pouvez configurer cette fonctionnalité que dans des pools d’hôtes existants. Cette fonctionnalité n’est pas disponible lorsque vous créez un pool d’hôtes.

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Pour utiliser le portail Azure afin de configurer l'option Démarrer la machine virtuelle à la connexion :

1. Ouvrez votre navigateur web et accédez au [portail Azure](https://portal.azure.com).

2. Sur le portail Azure, accédez à **Windows Virtual Desktop**.

3. Sélectionnez **Pools d'hôtes**, puis recherchez le pool d'hôtes qui contient les bureaux personnels auxquels vous avez attribué le rôle.

   >[!NOTE]
   > Le pool d'hôtes dans lequel vous configurez cette fonctionnalité doit contenir des bureaux personnels auxquels des rôles ont directement été attribués. Si les bureaux du pool d'hôtes ne sont pas correctement configurés, le processus de configuration ne fonctionnera pas.

4. Dans le pool d'hôtes, sélectionnez **Propriétés**. Sous **Démarrer la machine virtuelle à la connexion**, sélectionnez **Oui**, puis sélectionnez **Enregistrer** pour appliquer le paramètre instantanément.

    > [!div class="mx-imgBorder"]
    > ![Capture d'écran de la fenêtre Propriétés. L'option Démarrer la machine virtuelle à la connexion est surlignée en rouge.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>Utiliser PowerShell

Pour configurer ce paramètre avec PowerShell, vous devez vous assurer de disposer des noms du groupe de ressources et des pools d’hôtes que vous souhaitez configurer. Vous devez également installer le [module Azure PowerShell (version 2.1.0 ou ultérieure)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0).

Pour configurer le démarrage de machine virtuelle lors de la connexion à l’aide de PowerShell :

1. Ouvrez une fenêtre de commande PowerShell.

2. Pour activer le démarrage de machine virtuelle lors de la connexion, exécutez la cmdlet suivante :

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Pour désactiver le démarrage de machine virtuelle lors de la connexion, exécutez la cmdlet suivante :

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Expérience utilisateur

Dans des sessions classiques, le temps nécessaire à un utilisateur pour se connecter à une machine virtuelle libérée augmente, car la machine virtuelle a besoin de temps pour redémarrer, tout comme pour le démarrage d’un ordinateur physique. Le client Bureau à distance est muni d’un témoin permettant à l’utilisateur de savoir que le PC est sous tension pendant qu’il se connecte.

## <a name="troubleshooting"></a>Dépannage

Si la fonctionnalité rencontre des problèmes, nous vous suggérons d’utiliser la [fonctionnalité de diagnostics](diagnostics-log-analytics.md) de Windows Virtual Desktop pour identifier les problèmes. Si vous recevez un message d’erreur, soyez particulièrement attentif au contenu du message et copiez le nom de l’erreur quelque part pour référence.

Vous pouvez également utiliser [Azure Monitor pour Windows Virtual Desktop](azure-monitor.md) pour obtenir des suggestions sur la façon de résoudre les problèmes.

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes que la documentation relative à la résolution des problèmes ou la fonctionnalité de diagnostics n’a pas pu résoudre, consultez le [Forum aux questions à propos du démarrage de machine virtuelle lors de la connexion](start-virtual-machine-connect-faq.md).