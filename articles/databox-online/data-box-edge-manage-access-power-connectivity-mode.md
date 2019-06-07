---
title: Accès des appareils Edge de zone de données Azure, de puissance et de mode de connectivité | Microsoft Docs
description: Décrit comment gérer l’accès, de puissance et de mode de connectivité pour l’appareil de périphérie de zone de données Azure que vous aide à transférer des données vers Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 8937f4c47f0fa84d4ec371e951cff8a2fdaa8481
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476904"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Gérer l’accès, de puissance et de mode de connectivité pour votre zone de données Azure Edge

Cet article décrit comment gérer le mode d’accès, de puissance et de connectivité pour votre zone de données Azure Edge. Ces opérations sont effectuées sur l’interface utilisateur web locale ou le Portail Azure.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Gérer l’accès aux appareils
> * Gérer le mode de connectivité
> * Gérer la puissance


## <a name="manage-device-access"></a>Gérer l’accès aux appareils

L’accès à votre appareil Edge de zone de données est contrôlé par l’utilisation d’un mot de passe du périphérique. Vous pouvez modifier le mot de passe via l’interface utilisateur web locale. Vous pouvez également réinitialiser le mot de passe dans le portail Azure.

### <a name="change-device-password"></a>Modifier le mot de passe d’un appareil

Suivez ces étapes dans l’interface utilisateur locale pour modifier le mot de passe.

1. Sur l’interface utilisateur web locale, accédez à **Maintenance > Modification du mot de passe**.
2. Entrez le mot de passe actuel, puis le nouveau. Le mot de passe indiqué doit comporter entre 8 et 16 caractères, d’au moins trois types : majuscule, minuscule, chiffre et caractère spécial. Confirmez le nouveau mot de passe.

    ![Modifier le mot de passe](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Sélectionnez **Modifier le mot de passe**.
 
### <a name="reset-device-password"></a>Réinitialiser le mot de passe de périphérique

Le workflow de réinitialisation n’implique pas de se rappeler l’ancien mot de passe, ce qui est utile en cas de perte. Il s’effectue sur le Portail Azure.

1. Sur le Portail Azure, accédez à **Vue d’ensemble > Réinitialiser le mot de passe d’administrateur**.

    ![Réinitialiser le mot de passe](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Entrez le nouveau mot de passe, puis confirmez-le. Le mot de passe indiqué doit comporter entre 8 et 16 caractères, d’au moins trois types : majuscule, minuscule, chiffre et caractère spécial. Sélectionnez **réinitialiser**.

    ![Réinitialiser le mot de passe](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gérer l’accès aux ressources

Pour créer votre zone Edge/données zone passerelle de données, IoT Hub et ressource de stockage Azure, vous avez besoin d’autorisations en tant que contributeur ou supérieur au niveau du groupe de ressources. Vous devez également les fournisseurs de ressources correspondant à inscrire. Pour toutes les opérations qui impliquent des informations d’identification et la clé d’activation, les autorisations pour l’API Graph Azure Active Directory sont également requises. Ceux-ci sont décrits dans les sections suivantes.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Gérer les autorisations de l’API Microsoft Azure Active Directory Graph

Lorsque vous générez la clé d’activation pour l’appareil Edge de zone de données ou effectuer des opérations qui nécessitent des informations d’identification, vous avez besoin d’autorisations pour l’API Graph Azure Active Directory. Les opérations nécessitant des informations d’identification peut être :

-  Création d’un partage avec un compte de stockage associé.
-  Création d’un utilisateur qui peut accéder aux partages sur l’appareil.

Vous devez avoir un `User` accéder sur le client Active Directory que vous devez être en mesure de `Read all directory objects`. Vous ne peut pas être un utilisateur invité comme ils ne sont pas autorisés à `Read all directory objects`. Si vous êtes invité, puis les opérations telles que la génération d’une clé d’activation, la création d’un partage sur votre appareil Edge de zone de données, la création d’un utilisateur tout échoue.

Pour plus d’informations sur la façon de fournir l’accès aux utilisateurs pour l’API Graph Azure Active Directory, consultez [accès par défaut pour les administrateurs, utilisateurs et utilisateurs invités](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Inscrire des fournisseurs de ressources

Pour configurer une ressource dans Azure (dans le modèle Azure Resource Manager), vous avez besoin d’un fournisseur de ressources qui prend en charge la création de cette ressource. Par exemple, pour approvisionner une machine virtuelle, vous devez avoir un fournisseur de ressources « Microsoft.Compute » disponible dans l’abonnement.
 
Fournisseurs de ressources sont enregistrés sur le niveau de l’abonnement. Par défaut, tout nouvel abonnement Azure est déjà inscrit avec une liste de fournisseurs de ressources couramment utilisées. Le fournisseur de ressources 'Microsoft.DataBoxEdge' n’est pas inclus dans cette liste.

Vous n’avez pas besoin d’accorder des autorisations d’accès au niveau de l’abonnement pour les utilisateurs soient en mesure de créer des ressources telles que « Microsoft.DataBoxEdge » au sein de leurs groupes de ressources qu’ils ont des droits de propriétaire sur, tant que les fournisseurs de ressources pour ces ressources est déjà inscrit.

Avant d’essayer de créer n’importe quelle ressource, assurez-vous que le fournisseur de ressources est enregistré dans l’abonnement. Si le fournisseur de ressources n’est pas inscrit, vous devez vous assurer que l’utilisateur qui crée la nouvelle ressource dispose de droits suffisants pour inscrire le fournisseur de ressources requises du niveau d’abonnement. Si vous n’avez pas le fait aussi, vous verrez l’erreur suivante :

*L’abonnement <Subscription name> ne dispose pas des autorisations pour enregistrer les ou les fournisseurs de ressources : Microsoft.DataBoxEdge.*


Pour obtenir une liste de fournisseurs de ressources inscrit dans l’abonnement actuel, exécutez la commande suivante :

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Pour l’appareil de périphérie de zone de données, `Microsoft.DataBoxEdge` doit être inscrit. Pour inscrire `Microsoft.DataBoxEdge`, administrateur de l’abonnement doit exécuter la commande suivante :

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Pour plus d’informations sur la façon d’inscrire un fournisseur de ressources, consultez [résoudre des erreurs d’inscription du fournisseur de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Gérer le mode de connectivité

Mis à part le mode totalement connecté par défaut, votre appareil peut également exécuter en mode partiellement connecté ou déconnecté entièrement. :

- **Entièrement connecté** -c’est le mode normal par défaut dans lequel l’appareil fonctionne. Le téléchargement de cloud et le téléchargement de données est activée dans ce mode. Vous pouvez utiliser le portail Azure ou l’interface utilisateur web locale pour gérer l’appareil.

- **Partiellement déconnecté** – dans ce mode, l’appareil ne peut pas charger ou télécharger n’importe quel partage de données peuvent toutefois être gérées via le portail Azure.

    Ce mode est généralement utilisé sur un réseau satellite limité, dans l’objectif de réduire la consommation de bande passante réseau. Une consommation réseau minimale peut subsister pour les opérations de monitoring des appareils.

- **Déconnecté** – Dans ce mode, l’appareil est entièrement déconnecté du cloud ; les chargements et téléchargements cloud sont désactivés. L’appareil n’est gérable que sur l’interface utilisateur web locale.

    Ce mode est généralement utilisé pour mettre l’appareil hors connexion.

Pour modifier le mode de l’appareil, suivez ces étapes :

1. Sur l’interface utilisateur web locale de votre appareil, accédez à **Configuration > Paramètres cloud**.
2. Dans la liste déroulante, sélectionnez le mode que vous souhaitez faire fonctionner le périphérique dans. Vous pouvez sélectionner à partir de **entièrement connectée**, **partiellement connecté**, et **entièrement déconnecté**. Pour exécuter l’appareil en mode partiellement déconnecté, activez **Gestion sur le Portail Azure**.

    ![Mode de connectivité](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Gérer la puissance

Vous pouvez arrêter ou redémarrer votre appareil physique à l’aide de l’interface utilisateur web locale. Nous recommandons d’avant de redémarrer, mettre hors connexion les partages sur le serveur de données, puis sur l’appareil. Cette action réduit les risques d’altération des données.

1. Sur l’interface utilisateur web locale, accédez à **Maintenance > Paramètres d'alimentation**.
2. Sélectionnez **arrêt** ou **redémarrer** selon ce que vous voulez faire.

    ![Paramètres d’alimentation](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Lorsque vous êtes invité à confirmer l’opération, sélectionnez **Oui** pour continuer.

> [!NOTE]
> Si vous arrêtez l’appareil physique, vous devez actionner le bouton d’alimentation sur l’appareil pour l’activer.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer les partages](data-box-edge-manage-shares.md).