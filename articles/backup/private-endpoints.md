---
title: Points de terminaison privés
description: Apprenez à créer des points de terminaison privés pour le service Sauvegarde Azure et découvrez les scénarios où l’utilisation des points de terminaison privés contribue à maintenir la sécurité de vos ressources.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 7423157abbc0833394af055f5e31f724caa10b46
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224705"
---
# <a name="private-endpoints-for-azure-backup"></a>Points de terminaison privés pour le service Sauvegarde Azure

Le service Sauvegarde Azure vous permet de sauvegarder vos données dans vos coffres Recovery Services pour les restaurer ultérieurement en toute sécurité en utilisant des [points de terminaison privés](../private-link/private-endpoint-overview.md). Les points de terminaison privés utilisent une adresse IP privée de votre réseau virtuel (ou « VNet »), plaçant de fait le service dans votre réseau virtuel.

Cet article vous aidera à comprendre le processus de création de points de terminaison privés pour le service Sauvegarde Azure et les scénarios dans lesquels l’utilisation de points de terminaison privés contribue à maintenir la sécurité de vos ressources.

## <a name="before-you-start"></a>Avant de commencer

- Les points de terminaison privés ne peuvent être créés que pour les nouveaux coffres Recovery Services (qui ne contiennent pas d’éléments). Les points de terminaison privés doivent donc être créés avant que vous ne tentiez de protéger vos éléments en les plaçant dans un coffre.
- Un réseau virtuel peut contenir des points de terminaison privés pour plusieurs coffres Recovery Services. De son côté, un coffre Recovery Services peut être associé à plusieurs points de terminaison privés dans plusieurs réseaux virtuels. Toutefois, vous ne pouvez pas associer 1 coffre à plus de 12 points de terminaison privés.
- Lorsque vous associez un point de terminaison privé à un coffre, ce dernier est verrouillé. Cela veut dire qu’un coffre sera uniquement accessible (pour les sauvegardes et restaurations) sur les réseaux qui contiennent un point de terminaison privé associé à ce coffre. Si tous les points de terminaison privés associés au coffre sont supprimés, le coffre redevient accessible sur tous les réseaux.
- Une connexion de point de terminaison privée pour la sauvegarde utilise un total de 11 adresses IP privées dans votre sous-réseau, y compris celles utilisées par Sauvegarde Azure pour le stockage. Ce nombre peut être plus élevé (jusqu’à 25) pour certaines régions Azure. Nous vous suggérons donc d’avoir suffisamment d’adresses IP privées disponibles lorsque vous tentez de créer des points de terminaison privés pour la sauvegarde.
- Les coffres Recovery Services sont compatibles avec les services Sauvegarde Azure et Azure Site Recovery. Cependant, cet article traite uniquement de l’utilisation des points de terminaison privés pour le service Sauvegarde Azure.
- Azure Active Directory ne prend pas en charge les points de terminaison privés pour le moment. Par conséquent, les adresses IP et les noms de domaine complets requis pour le bon fonctionnement du service Azure Active Directory dans une région doivent bénéficier d’une autorisation d’accès sortant sur le réseau sécurisé lors de la sauvegarde de bases de données dans des machines virtuelles Azure et de la sauvegarde à l’aide de l’agent MARS. Vous pouvez aussi utiliser des balises de groupe de sécurité réseau (NSG) et des balises du service Pare-feu Azure pour autoriser l’accès à Azure AD, le cas échéant.
- Les réseaux virtuels avec des stratégies réseau ne sont pas compatibles avec les points de terminaison privés. Vous devez donc [désactiver les stratégies réseau](https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy) avant de continuer.
- Vous devrez réinscrire le fournisseur de ressources Recovery Services auprès de l’abonnement si vous l’avez enregistré avant le 1er mai 2020. Pour réinscrire le fournisseur, accédez à votre abonnement dans le portail Azure, accédez à **Fournisseur de ressources** dans la barre de navigation de gauche, sélectionnez **Microsoft.RecoveryServices**, puis sélectionnez **Réinscrire**.
- Les [restaurations inter-régions](backup-create-rs-vault.md#set-cross-region-restore) pour les sauvegardes de bases de données SQL et SAP HANA ne sont pas prises en charge si les points de terminaison privés sont activés dans le coffre.
- Lorsque vous déplacez un coffre Recovery Services utilisant déjà des points de terminaison privés vers un nouveau locataire, vous devez le mettre à jour pour recréer et reconfigurer son identité managée et créer des points de terminaison privés si nécessaire (qui doivent se trouver dans le nouveau locataire). Si cela n’est pas fait, les opérations de sauvegarde et de restauration échoueront. En outre, toutes les autorisations de contrôle d’accès en fonction du rôle (RBAC) configurées dans l’abonnement devront être reconfigurées.

## <a name="recommended-and-supported-scenarios"></a>Scénarios recommandés et pris en charge

Même si des points de terminaison privés sont activés dans un coffre, ils sont uniquement utilisés pour la sauvegarde et la restauration des charges de travail SQL et SAP HANA en cas de sauvegarde de machines virtuelles Azure et de sauvegarde à l’aide de l’agent MARS. Vous pouvez également utiliser le coffre pour la sauvegarde d’autres charges de travail (cependant, cela ne nécessiterait pas de point de terminaison privé). En plus de la sauvegarde des charges de travail SQL et SAP HANA et de la sauvegarde à l’aide de l’agent MARS, les points de terminaison privés servent également à effectuer des récupérations de fichiers pour la sauvegarde de machines virtuelles Azure. Pour plus d’informations, voir le tableau suivant :

| Sauvegarde de charges de travail dans une machine virtuelle Azure (SQL, SAP HANA) à l’aide de l’agent MARS | Nous vous recommandons d’utiliser des points de terminaison privés pour permettre la sauvegarde et la restauration sans avoir à ajouter à une liste d’autorisation les adresses IP ou les noms de domaine complets pour Sauvegarde Azure ou Stockage Azure depuis vos réseaux virtuels. Dans ce scénario, assurez-vous que les machines virtuelles hébergeant des bases de données SQL peuvent atteindre des adresses IP ou des noms de domaine complets Azure AD. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Sauvegarde des machines virtuelles Azure**                                         | La sauvegarde de machine virtuelle ne vous oblige pas à autoriser l’accès à des adresses IP ou des noms de domaine complets. Ainsi, les points de terminaison privés ne sont pas requis pour la sauvegarde et la restauration des disques.  <br><br>   Toutefois, la récupération de fichiers à partir d’un coffre contenant des points de terminaison privés est limitée aux réseaux virtuels qui contiennent un point de terminaison privé associé au coffre. <br><br>    Lorsque vous utilisez des disques non managés ACL, assurez-vous que le compte de stockage contenant les disques autorise l’accès à des **services Microsoft approuvés** s’il est ACL. |
| **Sauvegarde Azure Files**                                      | Les sauvegardes du service Azure Files sont stockées dans le compte de stockage local. Ainsi, vous n’avez pas besoin de points de terminaison privés pour la sauvegarde et la restauration. |

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>Prise en main de la création de points de terminaison privés pour Sauvegarde Azure

Les sections suivantes traitent des étapes de création et d’utilisation des points de terminaison privés pour Sauvegarde Azure au sein de vos réseaux virtuels.

>[!IMPORTANT]
> Nous vous recommandons vivement de suivre l’ordre des étapes établi dans ce document. Si vous ne le faites pas, le coffre rendu risque d’être incompatible avec l’utilisation des points de terminaison privés. Cela vous obligera à recommencer la procédure avec un nouveau coffre.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Les points de terminaison privés pour Sauvegarde Azure ne peuvent être créés que pour les coffres Recovery Services qui n’ont pas d’éléments protégés (ou qui n’ont pas été tentés d’être protégés ou inscrits auparavant). Nous vous suggérons donc de créer un coffre pour commencer. Pour plus d’informations sur la création d’un coffre, consultez [Créer et configurer un coffre Recovery Services](backup-create-rs-vault.md).

Consultez [cette section](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) pour savoir comment créer un coffre à l’aide du client Azure Resource Manager. Cela crée un coffre dont l’identité managée est déjà activée.

## <a name="enable-managed-identity-for-your-vault"></a>Activer une identité managée sur votre machine virtuelle

Les identités managées permettent au coffre de créer et d’utiliser des points de terminaison privés. Cette section traite de l’activation de l’identité managée pour votre coffre.

1. Accédez à votre coffre Recovery Services, puis au paramètre **Identity** (Identité).

    ![Capture d’écran montrant l’option d’activation de l’état de l’identité](./media/private-endpoints/identity-status-on.png)

1. Cliquez sur le bouton bascule sous **État** pour le définir sur la valeur **Activé**, puis sélectionnez **Enregistrer**.

1. Un **ID d’objet** est généré. Il correspond à l’identité managée du coffre.

    >[!NOTE]
    >Une fois activée, l’identité managée ne doit **pas** être désactivée (même temporairement). La désactivation de l’identité managée peut entraîner un comportement incohérent.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Accorder des autorisations au coffre pour créer des points de terminaison privés requis

Pour créer les points de terminaison privés requis pour le service Sauvegarde Azure, le coffre (ou plus exactement, l’identité managée du coffre) doit disposer d’autorisations sur les groupes de ressources suivants :

- Le groupe de ressources qui contient le réseau virtuel (VNet) cible.
- Le groupe de ressources dans lequel les points de terminaison privés doivent être créés.
- Le groupe de ressources qui contient Private DNS Zones, comme discuté en détail [ici](#create-private-endpoints-for-azure-backup)

Nous vous recommandons d’accorder le rôle **Contributor** (Contributeur) à ces trois groupes de ressources pour le coffre (identité managée). Les étapes suivantes expliquent comment effectuer cette opération pour un groupe de ressources particulier (cette opération doit être effectuée pour chacun des trois groupes de ressources) :

1. Accédez au groupe de ressources puis, dans la barre de gauche, accédez à **Access Control (IAM)** (Contrôle d'accès (Gestion des identités et des accès)).
1. Une fois dans **Access Control** (Contrôle d'accès), accédez à **Add a role assignment** (Ajouter une attribution de rôle).

    ![Ajouter une attribution de rôle](./media/private-endpoints/add-role-assignment.png)

1. Dans le volet **Add role assignment** (Ajouter une attribution de rôle), sélectionnez **Contributor** (Contributeur) en tant que **Role** (Rôle), puis utilisez le **Name** (Nom) du coffre en tant que **Principal**. Lorsque vous avez terminé, sélectionnez votre coffre, puis **Enregistrer**.

    ![Choisir un rôle et un principal](./media/private-endpoints/choose-role-and-principal.png)

Pour gérer les autorisations plus précisément, consultez la section [Créer manuellement des rôles et des autorisations](#create-roles-and-permissions-manually).

## <a name="create-private-endpoints-for-azure-backup"></a>Créer des points de terminaison privés pour Sauvegarde Azure

Cette section explique comment créer un point de terminaison privé pour votre coffre.

1. Accédez à votre coffre créé ci-dessus et rendez-vous dans **Connexions de point de terminaison privé** dans la barre de navigation de gauche. Sélectionnez **+Point de terminaison privé** en haut pour commencer à créer un nouveau point de terminaison privé pour ce coffre.

    ![Créer un point de terminaison privé](./media/private-endpoints/new-private-endpoint.png)

1. Une fois dans le processus **Create Private Endpoint** (Créer un point de terminaison privé), vous devez entrer des informations requises pour créer la connexion de votre point de terminaison privé.
  
    1. **Paramètres de base**: Entrez les informations de base de vos points de terminaison privés. La région doit être la même que celle du coffre et de la ressource sauvegardés.

        ![Capture d’écran montrant les champs d’informations de base à remplir](./media/private-endpoints/basics-tab.png)

    1. **Ressource** : Dans cet onglet, vous devez sélectionner la ressource PaaS pour laquelle vous souhaitez créer votre connexion. Sélectionnez **Microsoft.RecoveryServices/vaults** à partir du type de ressource pour l’abonnement souhaité. Lorsque vous avez terminé, choisissez le nom de votre coffre Recovery Services dans la section **Resource** (Ressource) et **AzureBackup** dans la section **Target sub-resource** (Sous-ressource cible).

        ![Sélectionner la ressource pour votre connexion](./media/private-endpoints/resource-tab.png)

    1. **Configuration** : Dans Configuration, spécifiez le réseau virtuel et le sous-réseau où vous souhaitez que le point de terminaison privé soit créé. Il s’agit du réseau virtuel sur lequel se trouve la machine virtuelle.

        Pour vous connecter de manière privée, vous devez disposer des enregistrements DNS requis. En fonction de la configuration de votre réseau, vous pouvez choisir l’une des options suivantes :

          - Intégrer votre point de terminaison privé à une zone DNS privée : sélectionnez **Oui** si vous souhaitez l’intégrer.
          - Utiliser votre serveur DNS personnalisé : sélectionnez **Non** si vous souhaitez utiliser votre propre serveur DNS.

        La gestion des enregistrements DNS pour ces deux options est [décrite plus loin](#manage-dns-records) dans cet article.

          ![Spécifier le réseau virtuel et le sous-réseau](./media/private-endpoints/configuration-tab.png)

    1. Si vous le souhaitez, vous pouvez ajouter des **balises** à votre point de terminaison privé.
    1. Passez à **Vérifier + créer** lorsque vous avez terminé la saisie des informations requises. Une fois la validation terminée, sélectionnez **Créer** pour créer le point de terminaison privé.

## <a name="approve-private-endpoints"></a>Approuver des points de terminaison privés

Si l’utilisateur qui crée le point de terminaison privé est également le propriétaire du coffre Recovery Services, le point de terminaison privé créé précédemment est approuvé automatiquement. Dans le cas contraire, le propriétaire du coffre doit approuver le point de terminaison privé avant de pouvoir l’utiliser. Cette section présente l’approbation manuelle des points de terminaison privés via le portail Azure.

Si vous souhaitez utiliser le client Azure Resource Manager pour approuver vos points de terminaison privés, consultez la section [Approbation manuelle des points de terminaison privés à l’aide du client Azure Resource Manager](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) client Azure Resource Manager pour utiliser le client Azure Resource Manager pour l’approbation des points de terminaison privés.

1. Accédez à votre coffre Recovery Services. Dans la barre de gauche, accédez à **Private endpoint connections** (Connexions de point de terminaison privé).
1. Sélectionnez la connexion de point de terminaison privé à approuver
1. Dans la barre supérieure, sélectionnez **Approve** (Approuver). Vous pouvez également sélectionner **Reject** (Rejeter) ou **Remove** (Supprimer) si vous souhaitez rejeter ou supprimer la connexion au point de terminaison.

    ![Capture d’écran montrant la procédure d’approbation d’un point de terminaison privé](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>Gestion des enregistrements DNS

Comme décrit précédemment, vous avez besoin des enregistrements DNS requis dans vos zones ou serveurs DNS privés afin de vous connecter de manière privée. Vous pouvez intégrer votre point de terminaison privé directement aux zones DNS privées Azure ou utiliser vos serveurs DNS personnalisés pour y parvenir, en fonction de vos préférences réseau. Cela devra être fait pour les trois services : Sauvegarde, Blobs et Files d’attente.

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>Lors de l’intégration de points de terminaison privés à des zones DNS privées Azure

Si vous choisissez d’intégrer votre point de terminaison privé à des zones DNS privées, Sauvegarde Azure ajoutera les enregistrements DNS requis. Vous pouvez afficher les zones DNS privées utilisées sous la rubrique **Configuration DNS** du point de terminaison privé. Si ces zones DNS ne sont pas présentes, elles sont créées automatiquement lors de la création du point de terminaison privé. Toutefois, vous devez vérifier que votre réseau virtuel (qui contient les ressources à sauvegarder) est correctement lié aux trois zones DNS privées, comme décrit ci-dessous.

![Configuration DNS dans la zone DNS privée Azure](./media/private-endpoints/dns-configuration.png)

#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>Valider les liaisons de réseau virtuel dans les zones DNS privées

Pour **chaque zone DNS privée** indiquée ci-dessus (pour Sauvegarde, Blobs et Files d’attente), procédez comme suit :

1. Accédez à l’option **Liens de réseau virtuel** respective dans la barre de navigation de gauche.
1. Vous devriez pouvoir voir une entrée pour le réseau virtuel pour lequel vous avez créé le point de terminaison privé, comme celui illustré ci-dessous :

    ![Réseau virtuel pour le point de terminaison privé](./media/private-endpoints/virtual-network-links.png)

1. Si vous ne voyez pas d’entrée, ajoutez un lien de réseau virtuel à toutes les zones DNS qui n’en ont pas.

    ![Ajouter un lien de réseau virtuel](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>Lors de l’utilisation d’un serveur DNS personnalisé ou de fichiers d’hôtes

Si vous utilisez vos serveurs DNS personnalisés, vous devez créer les zones DNS nécessaires et ajouter les enregistrements DNS requis par les points de terminaison privés à vos serveurs DNS. Pour les blobs et les files d’attente, vous pouvez également utiliser des redirecteurs conditionnels.

#### <a name="for-the-backup-service"></a>Pour le service Sauvegarde Azure

1. Sur votre serveur DNS, créez une zone DNS pour Sauvegarde Azure en respectant la convention d’affectation de noms suivante :

    |Zone |Service |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Sauvegarde        |

    >[!NOTE]
    > Dans le texte ci-dessus, `<geo>` fait référence au code de région (par exemple, *eus* et *ne* pour les régions USA Est et Europe Nord, respectivement). Consultez les listes suivantes pour connaître les codes de régions :
    >
    > - [Tous les clouds publics](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [Chine](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [Allemagne](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [Gouvernement des États-Unis](../azure-government/documentation-government-developer-guide.md)

1. Ensuite, nous devons ajouter les enregistrements DNS requis. Pour afficher les enregistrements qui doivent être ajoutés à la zone DNS de Sauvegarde Azure, accédez au point de terminaison privé que vous avez créé ci-dessus, puis accédez à l’option **Configuration DNS** sous la barre de navigation de gauche.

    ![Configuration DNS pour le serveur DNS personnalisé](./media/private-endpoints/custom-dns-configuration.png)

1. Ajoutez une entrée pour chaque nom de domaine complet et adresse IP affichés sous la forme d’enregistrements de type A dans votre zone DNS pour Sauvegarde Azure. Si vous utilisez un fichier d’hôte pour la résolution de noms, effectuez les entrées correspondantes dans le fichier d’hôte pour chaque adresse IP et nom de domaine complet selon le format suivant :

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>Comme indiqué dans la capture d’écran ci-dessus, les noms de domaine complets indiquent `xxxxxxxx.<geo>.backup.windowsazure.com` et non `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com`. Dans ce cas, veillez à inclure (et, le cas échéant, à ajouter) le `.privatelink.` conformément au format indiqué.

#### <a name="for-blob-and-queue-services"></a>Pour les services de Blob et de File d’attente

Pour les blobs et les files d’attente, vous pouvez utiliser des redirecteurs conditionnels ou créer des zones DNS sur votre serveur DNS.

##### <a name="if-using-conditional-forwarders"></a>En cas d’utilisation de redirecteurs conditionnels

Si vous utilisez des redirecteurs conditionnels, ajoutez des redirecteurs pour les noms de domaine complets des blobs et des files d’attente comme suit :

|FQDN  |IP  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>En cas d’utilisation de zones DNS privées

Si vous utilisez des zones DNS pour les blobs et les files d’attente, vous devez d’abord créer ces zones DNS et ajouter ultérieurement les enregistrements A requis.

|Zone |Service  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Objet blob     |
|`privatelink.queue.core.windows.net`     | File d'attente        |

Pour l’instant, nous ne créerons les zones pour les blobs et les files d’attente que lorsque nous utiliserons des serveurs DNS personnalisés. L’ajout d’enregistrements DNS sera effectué plus tard en deux étapes :

1. Lorsque vous inscrivez la première instance de sauvegarde, c’est-à-dire lorsque vous configurez la sauvegarde pour la première fois.
1. Lorsque vous exécutez la première sauvegarde.

Nous effectuerons ces étapes dans les sections suivantes.

## <a name="use-private-endpoints-for-backup"></a>Utiliser des points de terminaison privés pour Sauvegarde Azure

Après l’approbation des points de terminaison privés créés pour le coffre de votre réseau virtuel, vous pouvez commencer à les utiliser pour effectuer vos sauvegardes et restaurations.

>[!IMPORTANT]
>Avant de continuer, vérifiez que vous avez suivi toutes les étapes précédentes de ce document. Voici une liste des de ces étapes :
>
>1. Créer un (nouveau) coffre Recovery Services
>2. Activer le coffre pour utiliser l’identité managée affectée par le système
>3. Attribuer les autorisations appropriées à l’identité managée du coffre
>4. Créer un point de terminaison privé pour votre coffre
>5. Approuver le point de terminaison privé (s’il n’a pas été approuvé automatiquement)
>6. Vérifier que tous les enregistrements DNS sont correctement ajoutés (à l’exception des enregistrements de blob et de file d’attente pour les serveurs personnalisés, qui seront abordés dans les sections suivantes)

### <a name="check-vm-connectivity"></a>Vérifier la connectivité des machines virtuelles

Dans la machine virtuelle du réseau verrouillé, vérifiez les points suivants :

1. La machine virtuelle doit avoir accès à AAD.
2. Exécutez **nslookup** sur l’URL de sauvegarde (`xxxxxxxx.privatelink.<geo>.backup. windowsazure.com`) à partir de votre machine virtuelle pour garantir la connectivité. Cette opération doit renvoyer l’adresse IP privée attribuée dans votre réseau virtuel.

### <a name="configure-backup"></a>Configurer une sauvegarde

Une fois que vous vous êtes assuré que la liste de vérification ci-dessus et l’accès ont été correctement effectués, vous pouvez continuer à configurer la sauvegarde des charges de travail dans le coffre. Si vous utilisez un serveur DNS personnalisé, vous devez ajouter des entrées DNS pour les blobs et les files d’attente disponibles après la configuration de la première sauvegarde.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>Enregistrements DNS pour les blobs et les files d’attente (uniquement pour les fichiers d’hôtes/serveurs DNS personnalisés) après la première inscription

Une fois que vous avez configuré la sauvegarde pour au moins une ressource d’un coffre pour lequel un point de terminaison privé est activé, ajoutez les enregistrements DNS requis pour les blobs et les files d’attente, comme décrit ci-dessous.

1. Accédez à votre groupe de ressources, puis recherchez le point de terminaison privé que vous avez créé.
1. Hormis le nom du point de terminaison privé que vous avez donné, vous verrez deux autres points de terminaison privés en cours de création. Ces derniers commencent par `<the name of the private endpoint>_ecs` et sont suivis des suffixes `_blob` et `_queue` respectivement.

    ![Ressources de point de terminaison privé](./media/private-endpoints/private-endpoint-resources.png)

1. Accédez à chacun de ces points de terminaison privés. Dans l’option Configuration DNS pour chacun des deux points de terminaison privés, vous verrez un enregistrement avec un nom de domaine complet et une adresse IP. Ajoutez ces deux éléments à votre serveur DNS personnalisé, en plus de ceux décrits précédemment.
Si vous utilisez un fichier d’hôte, effectuez les entrées correspondantes dans le fichier d’hôte pour chaque adresse IP/nom de domaine complet selon le format suivant :

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![Configuration DNS de blobs](./media/private-endpoints/blob-dns-configuration.png)

Outre les éléments ci-dessus, une autre entrée est nécessaire après la première sauvegarde, dont il est question [plus loin](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup).

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Sauvegarde et restauration de charges de travail dans une machine virtuelle Azure (SQL et SAP HANA)

Une fois le point de terminaison privé créé et approuvé, aucune autre modification n’est requise côté client pour utiliser le point de terminaison privé (à moins que vous n’utilisiez des groupes de disponibilité SQL, cas que nous aborderons plus loin dans cette section). Toutes les communications et tous les transferts de données de votre réseau sécurisé vers le coffre sont effectués via le point de terminaison privé. Toutefois, si vous supprimez des points de terminaison privés associés au coffre après l’inscription d’un serveur (SQL ou SAP HANA), vous devez réinscrire le conteneur auprès du coffre. Vous n’avez pas besoin d’arrêter leur protection.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>Enregistrements DNS pour les blobs (uniquement pour les fichiers d’hôtes/serveurs DNS personnalisés) après la première sauvegarde

Une fois que vous avez exécuté la première sauvegarde et que vous utilisez un serveur DNS personnalisé (sans transfert conditionnel), il est probable que votre sauvegarde échoue. Si cela se produit :

1. Accédez à votre groupe de ressources, puis recherchez le point de terminaison privé que vous avez créé.
1. En plus des trois points de terminaison privés abordés précédemment, vous allez maintenant voir un quatrième point de terminaison privé dont le nom commence par `<the name of the private endpoint>_prot` et est suivi du suffixe `_blob`.

    ![Point de terminaison privé avec le suffixe « prot »](./media/private-endpoints/private-endpoint-prot.png)

1. Accédez à ce nouveau point de terminaison privé. Dans l’option Configuration DNS, vous verrez un enregistrement avec un nom de domaine complet et une adresse IP. Ajoutez-les à votre serveur DNS privé, en plus de ceux décrits précédemment.

    Si vous utilisez un fichier d’hôte, effectuez les entrées correspondantes dans le fichier d’hôte pour chaque adresse IP et nom de domaine complet selon le format suivant :

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>À ce stade, vous devriez être en mesure d’exécuter **nslookup** à partir de la machine virtuelle et de résoudre les adresses IP privées sur les URL de sauvegarde et de stockage du coffre.

### <a name="when-using-sql-availability-groups"></a>Lors de l’utilisation de groupes de disponibilité SQL

Lorsque vous utilisez des groupes de disponibilité (AG) SQL, vous devez configurer le transfert conditionnel dans le DNS AG personnalisé comme décrit ci-dessous :

1. Connectez-vous à votre contrôleur de domaine.
1. Sous l’application DNS, ajoutez des redirecteurs conditionnels pour les trois zones DNS (Sauvegarde, Blobs et Files d’attente) vers l’adresse IP de l’hôte 168.63.129.16 ou l’adresse IP du serveur DNS personnalisé, le cas échéant. Les captures d’écran suivantes illustrent le moment où vous effectuez un transfert vers l’adresse IP de l’hôte Azure. Si vous utilisez votre propre serveur DNS, remplacez-la par l’adresse IP de votre serveur DNS.

    ![Redirecteurs conditionnels dans Gestionnaire DNS](./media/private-endpoints/dns-manager.png)

    ![Nouveau redirecteur conditionnel](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>Sauvegarde et restauration par le biais de l’agent MARS

Lorsque vous utilisez l’agent MARS pour sauvegarder vos ressources locales, assurez-vous que votre réseau local (contenant vos ressources à sauvegarder) est homologué avec le réseau virtuel Azure qui contient un point de terminaison privé pour le coffre, afin de pouvoir l’utiliser. Vous pouvez ensuite continuer à installer l’agent MARS et configurer la sauvegarde comme indiqué ici. Toutefois, vous devez vous assurer que toutes les communications pour la sauvegarde s’effectuent uniquement par le biais du réseau homologué.

Cependant, si vous supprimez des points de terminaison privés pour le coffre après l’inscription d’un agent MARS, vous devez réinscrire le conteneur auprès du coffre. Vous n’avez pas besoin d’arrêter leur protection.

## <a name="deleting-private-endpoints"></a>Suppression de points de terminaison privés

Consultez [cette section](https://docs.microsoft.com/rest/api/virtualnetwork/privateendpoints/delete) pour savoir comment supprimer des points de terminaison privés.

## <a name="additional-topics"></a>Rubriques supplémentaires

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Utiliser le client Azure Resource Manager pour créer un coffre Recovery Services

Vous pouvez créer un coffre Recovery Services et activer son identité managée (l’activation de l’identité managée est requise, comme nous le verrons plus tard) à l’aide du client Azure Resource Manager. Un exemple de cette procédure est partagé ci-dessous :

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

Le fichier JSON ci-dessus doit avoir le contenu suivant :

Requête JSON :

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Réponse JSON :

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>Le coffre créé dans cet exemple via le client Azure Resource Manager est déjà créé avec une identité managée affectée par le système.

### <a name="managing-permissions-on-resource-groups"></a>Gestion des autorisations dans les groupes de ressources

L’identité managée pour le coffre doit disposer des autorisations suivantes dans le groupe de ressources et le réseau virtuel où les points de terminaison privés seront créés :

- `Microsoft.Network/privateEndpoints/*` : cette autorisation est nécessaire pour exécuter CRUD sur des points de terminaison privés dans le groupe de ressources. Elle doit être affectée au niveau du groupe de ressources.
- `Microsoft.Network/virtualNetworks/subnets/join/action` : cette autorisation est nécessaire sur le réseau virtuel où l’adresse IP privée est attachée au point de terminaison privé.
- `Microsoft.Network/networkInterfaces/read` : cette autorisation est nécessaire sur le groupe de ressources pour obtenir l’interface réseau créée pour le point de terminaison privé.
- Private DNS Zone Contributor Role (Rôle Contributeur de zone DNS privée) : ce rôle existe déjà et peut être utilisé pour fournir les autorisations `Microsoft.Network/privateDnsZones/A/*` et `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read`.

Vous pouvez utiliser l’une des méthodes suivantes pour créer des rôles avec les autorisations requises :

#### <a name="create-roles-and-permissions-manually"></a>Créer manuellement des rôles et des autorisations

Créez les fichiers JSON suivants et utilisez la commande PowerShell à la fin de la section pour créer des rôles :

//PrivateEndpointContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//NetworkInterfaceReaderRoleDef.json

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//PrivateEndpointSubnetContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Utiliser un script

1. Dans le portail Azure, démarrez le **Cloud Shell**, puis sélectionnez **Upload** (Charger) dans la fenêtre PowerShell.

    ![Capture d’écran montrant l’option Upload (Charger) de la fenêtre PowerShell](./media/private-endpoints/upload-file-in-powershell.png)

1. Chargez le script suivant : [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Accédez à votre dossier de démarrage (par exemple : `cd /home/user`)

1. Exécutez le script suivant :

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Les paramètres sont les suivants :

    - **subscription** : **SubscriptionId contenant le groupe de ressources dans lequel le point de terminaison privé du coffre doit être créé et le sous-réseau auquel le point de terminaison privé de l’archivage sera attaché

    - **vaultPEResourceGroup** : groupe de ressources dans lequel le point de terminaison privé de l’archivage sera créé

    - **vaultPESubnetResourceGroup** : groupe de ressources du sous-réseau auquel le point de terminaison privé sera joint

    - **vaultMsiName** : Nom du fichier MSI du coffre, qui est le même que **VaultName**

1. Terminez l’authentification et le script prendra le contexte de l’abonnement donné, fourni ci-dessus. Il créera les rôles appropriés si le locataire ne les contient pas, puis affectera des rôles au MSI du coffre.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Création d’un point de terminaison privé à l’aide d’Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Points de terminaison privés approuvés automatiquement

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Approbation manuelle des points de terminaison privés à l’aide du client Azure Resource Manager

1. Utilisez **GetVault** pour obtenir l’ID de connexion de point de terminaison privé de votre point de terminaison privé.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Cela retourne l’ID de connexion du point de terminaison privé. Vous pouvez récupérer le nom de la connexion à l’aide de la première partie de l’ID de connexion, comme suit :

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Dans la réponse, récupérez le paramètre **Private Endpoint Connection ID** (ID de connexion de point de terminaison) (et le paramètre **Private Endpoint Name** (Nom du point de terminaison privé), lorsque cela est nécessaire), remplacez-les par le code JSON et l’URI de Azure Resource Manager suivants, puis essayez de définir le paramètre Status (État) sur « Approved/Rejected/Disconnected » (« Approuvé/Rejeté/Déconnecté », comme illustré dans l’exemple ci-dessous :

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON :

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

## <a name="frequently-asked-questions"></a>Forum aux questions

Q. Puis-je créer un point de terminaison privé pour un coffre de sauvegarde existant ?<br>
R. Non, les points de terminaison privés ne peuvent être créés que pour les nouveaux coffres de sauvegarde. Le coffre ne doit donc pas contenir d’éléments protégés. En fait, aucune tentative de protection des éléments dans le coffre ne peut être effectuée avant de créer des points de terminaison privés.

Q. J’ai essayé de protéger un élément dans mon coffre, mais l’opération a échoué et le coffre ne contient toujours aucun élément protégé. Puis-je créer des points de terminaison privés pour ce coffre ?<br>
R. Non, le coffre ne doit pas avoir subi de tentatives de protection.

Q. J’ai un coffre qui utilise des points de terminaison privés pour la sauvegarde et la restauration. Puis-je ajouter ou supprimer ultérieurement des points de terminaison privés pour ce coffre, même si des éléments de sauvegarde y sont protégés ?<br>
R. Oui. Si vous avez déjà créé des points de terminaison privés pour un coffre et des éléments de sauvegarde protégés, vous pouvez ajouter ou supprimer ultérieurement des points de terminaison privés en fonction des besoins.

Q. Le point de terminaison privé du service Sauvegarde Azure peut-il également être utilisé pour le service Azure Site Recovery ?<br>
R. Non, le point de terminaison privé du service Sauvegarde ne peut être utilisé que pour le service Sauvegarde Azure. Vous devez créer un nouveau point de terminaison privé pour le service Azure Site Recovery, si cela est pris en charge par le service.

Q. Je n’ai pas suivi l’une des étapes de cet article, mais j’ai pu protéger ma source de données. Puis-je continuer à utiliser des points de terminaison privés ?<br>
R. Si vous ne suivez pas chaque étape de cet article et que vous continuez à protéger des éléments, le coffre risque de ne pas pouvoir utiliser des points de terminaison privés. C’est pourquoi nous vous recommandons de vous référer à cette liste de vérification avant de continuer à protéger les éléments.

Q. Puis-je utiliser mon propre serveur DNS au lieu d’utiliser la zone DNS privée Azure ou une zone DNS privée intégrée ?<br>
R. Oui, vous pouvez utiliser vos propres serveurs DNS. Toutefois, assurez-vous que tous les enregistrements DNS requis sont ajoutés comme indiqué dans cette section.

Q. Dois-je suivre des étapes supplémentaires sur mon serveur après avoir suivi toutes les procédures de cet article ?<br>
R. Après avoir suivi toutes les procédures de cet article, vous avez besoin de rien d’autre pour utiliser des points de terminaison privés pour la sauvegarde et la restauration.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur toutes les [fonctionnalités de sécurité de Sauvegarde Azure](security-overview.md).
