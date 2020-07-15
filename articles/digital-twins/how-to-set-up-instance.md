---
title: Créer une instance Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer une instance du service Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4cac7a3f663d9ede966b8d6e5753c48629049dcd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057481"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Configurer une instance Azure Digital Twins.

Cet article vous guide dans les étapes de base pour configurer une nouvelle instance Azure Digital Twins. Celles-ci comprennent la création de l’instance et l’affectation d’autorisations [Azure Active Directory (AAD](../active-directory/fundamentals/active-directory-whatis.md)) à l’instance pour vous-même.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Configurer une instance Azure Digital Twins.

Ensuite, vous allez créer un groupe de ressources Azure à utiliser dans cette procédure. Ensuite, vous pouvez **créer une instance d’Azure Digital Twins** à l’intérieur de ce groupe de ressources. 

Vous devez également fournir un nom pour votre instance, et choisir une région pour le déploiement. Pour connaître les régions qui prennent en charge Azure Digital Twins, visitez [Disponibilité des produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

>[!NOTE]
> Le nom de la nouvelle instance doit être unique dans la région (ce qui signifie que, si une autre instance Azure Digital Twins dans cette région utilise déjà le nom que vous choisissez, vous devrez choisir un autre nom).

Créez le groupe de ressources et l’instance avec les commandes suivantes :

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

Le résultat de ces commandes ressemble à ceci, en générant des informations sur les ressources que vous avez créées :

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="Fenêtre Commande montrant la création réussie d’un groupe de ressources et d’une instance Azure Digital Twins":::

Notez les valeurs *hostName*, *name* et *resourceGroup* de l’instance Azure Digital Twins fournies dans la sortie. Il s’agit de toutes les valeurs clés dont vous pouvez avoir besoin quand vous continuez à travailler avec votre instance Azure Digital Twins pour configurer l’authentification et les ressources Azure associées.

> [!TIP]
> Vous pouvez afficher ces propriétés, ainsi que toutes les propriétés de votre instance, à tout moment en exécutant `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

### <a name="assign-azure-active-directory-permissions"></a>Attribuer des autorisations Azure Active Directory

Azure Digital Twins utilise [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) pour le contrôle d’accès en fonction du rôle (RBAC). Cela signifie qu’avant de pouvoir effectuer des appels de plan de données à votre instance Azure Digital Twins, vous devez d’abord vous attribuer un rôle avec ces autorisations.

Pour pouvoir utiliser Azure Digital Twins avec une application cliente, vous devez également vous assurer que votre application cliente peut s’authentifier auprès d’Azure Digital Twins. Pour ce faire, vous devez configurer une inscription d’application Azure Active Directory (AAD), en procédant de la manière décrite dans [Procédure : Authentifier une application cliente](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Vous attribuer un rôle

Créez une attribution de rôle pour vous-même dans l’instance Azure Digital Twins, en utilisant l’adresse e-mail associée au locataire AAD sur votre abonnement Azure. 

Pour pouvoir effectuer cette opération, vous devez être désigné comme propriétaire dans votre abonnement Azure. Vous pouvez vérifier que c’est le cas en exécutant la commande `az role assignment list --assignee <your-Azure-email>` et en vérifiant dans la sortie que *roleDefinitionName* a la valeur *Owner*. Si une autre valeur que *Owner* est définie, y compris *Contributor*, veuillez demander à l’administrateur de votre abonnement qui peut accorder des autorisations dans votre abonnement d’élever votre rôle.

En tant que propriétaire de l’abonnement, vous pouvez utiliser la commande suivante pour assigner votre utilisateur à un rôle de propriétaire pour votre instance Azure Digital Twins :

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Les informations générées par cette commande décrivent l’attribution de rôle que vous avez créée.

> [!TIP]
> Si vous recevez un message d’erreur *400 : BadRequest*, exécutez la commande suivante pour obtenir l’*ObjectID* pour votre utilisateur :
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Ensuite, répétez la commande d’attribution de rôle en utilisant l’*ID d’objet* de votre utilisateur à la place de votre e-mail.

Vous disposez maintenant d’une instance Azure Digital Twins opérationnelle.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer et authentifier une application cliente pour qu’elle fonctionne avec votre instance :
* [Guide pratique pour Authentifier une application cliente](how-to-authenticate-client.md).
