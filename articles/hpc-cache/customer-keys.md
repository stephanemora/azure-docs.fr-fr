---
title: Utiliser des clés gérées par le client pour chiffrer des données dans Azure HPC Cache
description: Comment utiliser Azure Key Vault avec Azure HPC Cache pour contrôler l’accès à la clé de chiffrement au lieu d’utiliser les clés de chiffrement gérées par Microsoft par défaut
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/15/2021
ms.author: v-erkel
ms.openlocfilehash: dbb2834d6d51f2555da05863c606b4678b9a17b7
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772284"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Utiliser des clés de chiffrement gérées par le client pour Azure HPC Cache

Vous pouvez utiliser Azure Key Vault pour contrôler la propriété des clés utilisées pour chiffrer vos données dans Azure HPC Cache. Cet article explique comment utiliser des clés gérées par le client pour le chiffrement des données en cache.

> [!NOTE]
> Toutes les données stockées dans Azure, y compris sur les caches de disque, sont chiffrées au repos à l’aide de clés gérées par Microsoft par défaut. Vous devez suivre les étapes de cet article uniquement si vous souhaitez gérer les clés utilisées pour chiffrer vos données.

Azure HPC Cache est également protégé par le [chiffrement de l’hôte de machine virtuelle](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) sur les disques gérés qui contiennent vos données mises en cache, même si vous ajoutez une clé de client pour les caches de disque. L’ajout d’une clé gérée par le client pour le double chiffrement offre un niveau supplémentaire de sécurité aux clients ayant des besoins élevés en matière de sécurité. Pour plus d’informations, lisez [Chiffrement côté serveur de stockage sur disque Azure](../virtual-machines/disk-encryption.md).

Il y a trois étapes pour activer le chiffrement avec clé gérée par le client pour Azure HPC Cache :

1. Configurez un coffre Azure Key Vault pour stocker les clés.
1. Lorsque vous créez le cache Azure HPC Cache, choisissez le chiffrement avec clé gérée par le client et spécifiez le coffre de clés et la clé à utiliser. Si vous le souhaitez, fournissez une identité managée pour le cache à utiliser pour accéder au coffre de clés.

   Selon les choix que vous faites au cours de cette étape, il se peut que vous puissiez ignorer l’étape 3. Pour plus d’informations, consultez [Choisir une option d’identité managée pour le cache](#choose-a-managed-identity-option-for-the-cache).

1. Si vous utilisez une **identité managée affectée par le système** ou une **identité affectée par l’utilisateur qui n’est pas configurée avec l’accès au coffre de clés** : accédez au cache nouvellement créé et autorisez-le à accéder au coffre de clés.

   Si l’identité managée n’a pas encore accès à Azure Key Vault, votre chiffrement n’est pas complètement configuré tant que vous l’avez autorisé à partir du cache nouvellement créé (étape 3).

   Si vous utilisez une identité managée par le système, l’identité est créée lors de la création du cache. Vous devez transmettre l’identité du cache au coffre de clés pour en faire un utilisateur autorisé après la création du cache.

   Vous pouvez ignorer cette étape si vous attribuez une identité managée par l’utilisateur, qui a déjà accès au coffre de clés.

Après avoir créé le cache, vous ne pouvez plus passer d’une clé gérée par le client à une clé gérée par Microsoft. Toutefois, si votre cache utilise des clés gérées par le client, vous pouvez [modifier](#update-key-settings) la clé de chiffrement, la version de la clé et le coffre de clés le cas échéant.

## <a name="understand-key-vault-and-key-requirements"></a>Comprendre les conditions requises en matière de clés et de coffre de clés

Le coffre de clés et la clé doivent répondre à ces conditions pour fonctionner avec Azure HPC Cache.

Propriétés du coffre de clés :

* **Abonnement** : utilisez le même abonnement que celui utilisé pour le cache.
* **Région** : le coffre de clés doit se trouver dans la même région que le cache Azure HPC Cache.
* **Niveau tarifaire** : le niveau standard est suffisant pour une utilisation avec Azure HPC Cache.
* **Suppression réversible** : Azure HPC Cache active la suppression réversible si elle n’est pas déjà configurée sur le coffre de clés.
* **Protection contre le vidage** : la protection contre le vidage doit être activée.
* **Stratégie d’accès** : les paramètres par défaut sont suffisants.
* **Connectivité réseau** : Azure HPC Cache doit être en mesure d’accéder au coffre de clés, quels que soient les paramètres de point de terminaison que vous choisissez.

Propriétés de la clé :

* **Type de clé** : RSA
* **Taille de clé RSA** : 2048
* **Activée** : oui

Autorisations d’accès au coffre de clés :

* L’utilisateur qui crée le cache Azure HPC Cache doit disposer d’autorisations équivalentes à celles du [rôle Contributeur de Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor). Les mêmes autorisations sont requises pour configurer et gérer Azure Key Vault.

  Pour plus d’informations, consultez [Sécuriser l’accès à un coffre de clés](../key-vault/general/security-features.md).

## <a name="choose-a-managed-identity-option-for-the-cache"></a>Choisir une option d’identité managée pour le cache
<!-- check for cross-references from here and create -->

Votre HPC Cache utilise les informations d’identification de son identité managée pour se connecter au coffre de clés.

Azure HPC Cache peut utiliser deux sortes d’identités managées :

* **Identité managée affectée par le système** : identité unique créée automatiquement pour votre cache. Cette identité managée n’existe que le temps que le HPC Cache existe, et ne peut pas être gérée ou modifiée directement.

* **Identité managée affectée par l’utilisateur** : informations d’identification d’une identité autonome que vous gérez séparément du cache. Vous pouvez configurer une identité managée affectée par l’utilisateur disposant exactement de l’accès que vous souhaitez, et l’utiliser dans plusieurs services HPC Cache.

Si vous n’affectez pas d’identité managée au cache lors de sa création, Azure crée automatiquement une identité managée affectée par le système pour le cache.

Avec une identité managée affectée par l’utilisateur, vous pouvez fournir une identité qui a déjà accès à votre coffre de clés (par exemple, elle a été ajoutée à une stratégie d’accès au coffre de clés ou a un rôle RBAC Azure autorisant l’accès). Si vous utilisez une identité affectée par le système ou fournissez une identité managée dépourvue d’accès, vous devez demander l’accès à partir du cache après sa création. Il s’agit d’une étape manuelle décrite ci-dessous à l’[étape 3](#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed).

* En savoir plus sur les [Identités managées](../active-directory/managed-identities-azure-resources/overview.md)

* Découvrez les [Principes fondamentaux d’Azure Key Vault](../key-vault/general/basic-concepts.md)

## <a name="1-set-up-azure-key-vault"></a>1. Configurer Azure Key Vault

Vous pouvez configurer un coffre de clés et une clé avant de créer le cache, ou vous pouvez le faire dans le cadre de la création du cache. Assurez-vous que ces ressources remplissent les conditions requises décrites [ci-dessus](#understand-key-vault-and-key-requirements).

Au moment de la création du cache, vous devez spécifier un coffre, une clé et une version de clé à utiliser pour le chiffrement du cache.

Pour plus d’informations, lisez la [documentation Azure Key Vault](../key-vault/general/overview.md).

> [!NOTE]
> Le coffre Azure Key Vault doit utiliser le même abonnement et se trouver dans la même région que le cache Azure HPC Cache. Assurez-vous que la région que vous choisissez [prend en charge les deux produits](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Créer le cache avec les clés gérées par le client activées

Vous devez spécifier la source de la clé de chiffrement lors de la création de votre cache Azure HPC Cache. Suivez les instructions de l’article [Créer un cache Azure HPC Cache](hpc-cache-create.md), puis spécifiez le coffre de clés et la clé dans la page **Clés de chiffrement du disque**. Vous pouvez créer un coffre de clés et une clé lors de la création du cache.

> [!TIP]
> Si la page **Clés de chiffrement du disque** ne s’affiche pas, assurez-vous que votre cache se trouve dans l’une des [régions prises en charge](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

![Capture de l’écran des clés de chiffrement de disque, qui fait partie de l’interface de création du cache dans le portail.](media/customer-keys-populated.png)

L’utilisateur qui crée le cache doit avoir des privilèges égaux ou supérieurs à ceux du [rôle Contributeur de Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor).

1. Cliquez sur le bouton pour activer les clés gérées de manière privée. Une fois que vous avez modifié ce paramètre, les paramètres du coffre de clés s’affichent.

1. Cliquez sur **Sélectionner un coffre de clés** pour ouvrir la page de sélection de la clé. Choisissez ou créez le coffre de clés et la clé pour le chiffrement des données sur les disques de ce cache.

   Si votre coffre Azure Key Vault n’apparaît pas dans la liste, vérifiez les conditions requises suivantes :

   * Le cache se trouve-t-il dans le même abonnement que le coffre de clés ?
   * Le cache se trouve-t-il dans la même région que le coffre de clés ?
   * Existe-t-il une connectivité réseau entre le Portail Azure et le coffre de clés ?

1. Après avoir sélectionné un coffre, sélectionnez la clé individuelle parmi les options disponibles ou créez une nouvelle clé. La clé doit être une clé RSA 2048 bits.

1. Spécifiez la version de la clé sélectionnée. En savoir plus sur le contrôle de version dans la [documentation Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Ces paramètres sont facultatifs :

* Cochez la case **Toujours utiliser la version actuelle de la clé** si vous souhaitez utiliser la [permutation automatique des clés](../virtual-machines/disk-encryption.md#automatic-key-rotation-of-customer-managed-keys).

* Si vous voulez utiliser une identité managée spécifique pour ce cache, dans la section **Identités managées**, sélectionnez **Affectée par l’utilisateur**, puis sélectionnez l’identité à utiliser. Pour obtenir de l’aide, consultez la [documentation sur les identités managées](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

  > [!TIP]
  > Une identité managée affectée par l’utilisateur peut simplifier la création du cache si vous transmettez une identité déjà configurée pour accéder à votre coffre de clés. Avec une identité managée affectée par le système, vous devez accomplir une étape supplémentaire après la création du cache, afin d’autoriser l’identité affectée par le système nouvellement créée du cache à utiliser votre coffre de clés.

  > [!NOTE]
  > Vous pouvez modifier l’identité affectée après la création du cache.

Poursuivez avec le reste des spécifications et créez le cache comme décrit dans [Créer un cache Azure HPC Cache](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache-if-needed"></a>3. Autoriser le chiffrement Azure Key Vault à partir du cache (si nécessaire)
<!-- header is linked from create article, update if changed -->

> [!NOTE]
> Cette étape n’est pas requise si vous avez fourni une identité managée affectée par l’utilisateur ayant accès au coffre de clés lors de la création du cache.

Après quelques minutes, le nouveau cache Azure HPC Cache s’affiche dans votre Portail Azure. Accédez à la page **Vue d’ensemble** pour l’autoriser à accéder à votre coffre Azure Key Vault et à activer le chiffrement avec clé gérée par le client.

> [!TIP]
> Le cache peut apparaître dans la liste des ressources avant l’effacement des messages « déploiement en cours ». Vérifiez votre liste de ressources après une minute ou deux au lieu d’attendre une notification de réussite.
>
> Vous devez autoriser le chiffrement dans les 90 minutes suivant la création du cache. Si vous n’effectuez pas cette étape, le cache expire et échoue. Un cache ayant échoué doit être recréé, il ne peut pas être corrigé.

Le cache affiche l’État **En attente de la clé**. Cliquez sur le bouton **Activer le chiffrement** en haut de la page pour autoriser le cache à accéder au coffre de clés spécifié.

![Capture d’écran de la page de vue d’ensemble du cache dans le portail, avec, en évidence, le bouton Activer le chiffrement (ligne supérieure) et État : En attente de la clé.](media/waiting-for-key.png)

Cliquez sur **Activer le chiffrement**, puis sur le bouton **Oui** pour autoriser le cache à utiliser la clé de chiffrement. Cette action active également la suppression réversible et la protection contre le vidage (si elles ne sont pas déjà activées) sur le coffre de clés.

![Capture d’écran de la page de vue d’ensemble du cache dans le portail, avec un message de bannière en haut qui demande à l’utilisateur d’activer le chiffrement en cliquant sur Oui.](media/enable-keyvault.png)

Une fois que le cache a demandé l’accès au coffre de clés, il peut créer et chiffrer les disques qui stockent les données en cache.

Une fois le chiffrement autorisé, Azure HPC Cache passe par plusieurs minutes supplémentaires de configuration pour créer les disques chiffrés et l’infrastructure associée.

## <a name="update-key-settings"></a>Mettre à jour les paramètres de clé

Vous pouvez modifier le coffre de clés, la clé ou la version de clé de votre cache à partir du Portail Azure. Cliquez sur le lien des paramètres **Chiffrement** du cache pour ouvrir la page **Paramètres des clés du client**.

Vous ne pouvez pas modifier un cache pour passer de clés gérées par le client à des clés gérées par le système.

![Capture d’écran de la page « Paramètres des clés du client », accessible en cliquant sur Paramètres > Chiffrement dans la page Cache du portail Azure.](media/change-key-click.png)

Cliquez sur le lien **Modifier la clé**, puis cliquez sur **Modifier le coffre de clés, la clé ou la version** pour ouvrir le sélecteur de clé.

![Capture d’écran de la page « Sélectionner une clé à partir d’Azure Key Vault », avec trois sélecteurs de liste déroulante pour choisir un coffre de clés, une clé et une version.](media/select-new-key.png)

Les coffres de clés qui figurent dans le même abonnement et la même région que ce cache sont affichés dans la liste.

Une fois que vous avez choisi les nouvelles valeurs de clé de chiffrement, cliquez sur **Sélectionner**. Une page de confirmation s’affiche avec les nouvelles valeurs. Cliquez sur **Enregistrer** pour finaliser la sélection.

![Capture d’écran de la page de confirmation, avec le bouton Enregistrer en haut à gauche.](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>En savoir plus sur les clés gérées par le client dans Azure

Ces articles expliquent plus en détail l’utilisation d’Azure Key Vault et des clés gérées par le client pour chiffrer les données dans Azure :

* [Vue d’ensemble du chiffrement du stockage Azure](../storage/common/storage-service-encryption.md)
* [Chiffrement de disque avec des clés gérées par le client](../virtual-machines/disk-encryption.md#customer-managed-keys) : documentation pour l’utilisation d’Azure Key Vault avec de disques managés, qui est un scénario similaire à Azure HPC Cache

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez créé le cache Azure HPC Cache et autorisé le chiffrement basé sur Key Vault, continuez à configurer votre cache en lui donnant accès à vos sources de données.

* [Ajouter des cibles de stockage](hpc-cache-add-storage.md)
