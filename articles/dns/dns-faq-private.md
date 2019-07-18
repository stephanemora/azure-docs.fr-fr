---
title: FAQ sur Azure Private DNS
description: Questions fréquentes (FAQ) sur Azure Private DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 480cf22491dbbfcb9fe1961b5c9a7aa6fe12a0cb
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274040"
---
# <a name="azure-private-dns-faq"></a>FAQ sur Azure Private DNS

> [!IMPORTANT]
> Azure Private DNS est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS prend-il en charge les domaines privés ?

La prise en charge de domaines « privés » est implémentée à l’aide de zones Azure Private DNS. Les zones du DNS privé sont gérées à l’aide des mêmes outils que les zones Azure Private DNS accessibles sur Internet. Elles peuvent être résolues uniquement à partir de vos réseaux virtuels spécifiés. Pour plus d’informations, consultez la [Vue d’ensemble](private-dns-overview.md).

Pour plus d’informations sur les autres options DNS internes dans Azure, consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS Private Zones fonctionne-il d’une région Azure à une autre ?

Oui. La fonctionnalité Private Zones est prise en charge pour la résolution DNS entre les réseaux virtuels de différentes régions Azure. Elle fonctionne même sans l’appairage explicite des réseaux virtuels. Tous les réseaux virtuels doivent être spécifiés en tant que réseaux virtuels de résolution pour la zone privée. Les clients peuvent avoir besoin que les réseaux virtuels soient appairés pour que le trafic TCP/HTTP circule d’une région à une autre.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>La connectivité à Internet à partir de réseaux virtuels est-elle nécessaire pour les zones privées ?

Non. Les zones privées fonctionnent avec les réseaux virtuels. Les clients les utilisent pour gérer les domaines pour les machines virtuelles ou d’autres ressources dans et entre les réseaux virtuels. Une connectivité à Internet n’est pas nécessaire pour la résolution de noms.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>La même zone privée peut-elle être utilisée pour plusieurs réseaux virtuels pour la résolution ?

Oui. Vous pouvez associer jusqu’à 1 000 réseaux virtuels de résolution à une même zone privée.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Un réseau virtuel qui appartient à un autre abonnement peut-il être ajouté en tant que réseau virtuel lié à une zone privée ?

Oui. Vous devez disposer d'une autorisation d'écriture sur les réseaux virtuels ainsi que sur la zone DNS privée. L’autorisation d’accès en écriture peut être accordée à plusieurs rôles RBAC. Par exemple, le rôle RBAC Contributeur de réseaux classiques dispose d’autorisations d’écriture sur les réseaux virtuels. Pour plus d’informations sur les rôles RBAC, consultez [Contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Les enregistrements DNS de machines virtuelles inscrits automatiquement dans une zone privée sont-ils supprimés automatiquement quand vous supprimez la machine virtuelle ?

Oui. Si vous supprimez une machine virtuelle dans un réseau virtuel lié alors que l’inscription automatique est activé, les enregistrements inscrits sont automatiquement supprimés.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Un enregistrement de machine virtuelle inscrit automatiquement dans une zone privée à partir d’un réseau virtuel lié peut-il être supprimé manuellement ?

Oui. Vous pouvez remplacer les enregistrements DNS inscrits automatiquement par un enregistrement DNS créé manuellement dans la zone. La question et la réponse suivantes traitent de ce sujet.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Que se passe-t-il quand nous essayons de créer manuellement un enregistrement DNS dans une zone privée qui a le même nom d’hôte qu’une machine virtuelle existante inscrite automatiquement sur un réseau virtuel lié ?

Vous essayez de créer manuellement un enregistrement DNS dans une zone privée qui a le même nom d’hôte qu’une machine virtuelle inscrite automatiquement existant sur un réseau virtuel lié. Dans ce cas, le nouvel enregistrement DNS remplace l’enregistrement de machine virtuelle inscrit automatiquement. Si vous essayez de supprimer à nouveau cet enregistrement DNS créé manuellement de la zone, la suppression réussit. L’inscription automatique se produit à nouveau tant que la machine virtuelle existe encore et qu’une adresse IP privée lui est attachée. L’enregistrement DNS est automatiquement recréé dans la zone.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Que se passe-t-il quand nous dissocions un réseau virtuel lié d’une zone privée ? Les enregistrements de machines virtuelles du réseau virtuel inscrits automatiquement sont-ils également supprimés de la zone ?

Oui. Pour dissocier un réseau virtuel lié d’une zone privée, vous mettez à jour la zone DNS pour supprimer le réseau virtuel lié associé. Dans ce processus, les enregistrements de machines virtuelles qui ont été inscrits automatiquement sont supprimés de la zone.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Que se passe-t-il quand nous supprimons un réseau virtuel lié qui est lié à une zone privée ? Devons-nous mettre manuellement à jour la zone privée afin de dissocier le réseau virtuel en tant que réseau virtuel lié de la zone ?

Oui. Quand vous supprimez un réseau virtuel lié sans le dissocier au préalable d’une zone privée, votre opération de suppression réussit. Toutefois, le réseau virtuel n’est pas automatiquement dissocié de votre zone privée, le cas échéant. Vous devez dissocier manuellement le réseau virtuel de la zone privée. C’est pourquoi vous devez dissocier votre réseau virtuel de votre zone privée avant de le supprimer.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>La résolution DNS à l’aide du nom de domaine complet (FQDN) par défaut (internal.cloudapp.net) fonctionne-t-elle même quand une zone privée (par exemple, private.contoso.com) est liée à un réseau virtuel ?

Oui. La fonctionnalité Private Zones ne remplace pas les résolutions DNS par défaut à l’aide de la zone internal.cloudapp.net fournie par Azure. Elle est proposée en tant qu’amélioration ou fonctionnalité supplémentaire. Que vous vous appuyiez sur la zone internal.cloudapp.net fournie par Azure ou sur votre propre zone privée, utilisez le nom de domaine complet de la zone par rapport à laquelle vous souhaitez effectuer la résolution.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Le suffixe DNS sur les machines virtuelles au sein d’un réseau virtuel lié est-il remplacé par celui de la zone privée ?

Non. Le suffixe DNS sur les machines virtuelles de votre réseau virtuel lié reste le suffixe par défaut fourni par Azure (« *.internal.cloudapp.net »). Vous pouvez changer manuellement ce suffixe DNS sur vos machines virtuelles en le remplaçant par celui de la zone privée.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Quelles sont les limites d’utilisation d’Azure Private DNS ?

Les limites par défaut suivantes s’appliquent quand vous utilisez Azure Private DNS.

| Ressource | Limite par défaut |
| --- | --- |
|Zones DNS privées par abonnement|1 000|
|Jeux d’enregistrements par zone DNS privée|25 000|
|Enregistrements par jeu d’enregistrements|20|
|Liens de réseau virtuel par zone DNS privée|1 000|
|Liens de réseau virtuel par zone DNS privée avec l’inscription automatique activée|100|
|Nombre de zones DNS privées auxquelles un réseau virtuel peut être lié avec l’inscription automatique activée|1|
|Nombre de zones DNS privées auxquelles un réseau virtuel peut être lié|1 000|

## <a name="is-there-portal-support-for-private-zones"></a>Existe-t-il une prise en charge du portail pour les zones privées ?

Oui, et les zones privées qui sont déjà créées par le biais des API, de PowerShell, de l’interface CLI et des SDK sont visibles sur le portail Azure.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Pourquoi mes zones DNS privées existantes n’apparaissent-elles pas dans le nouveau portail ?

Dans le cadre de l’actualisation de la préversion, nous avons fourni un nouveau modèle de ressource pour les zones privées Azure DNS. Vos zones DNS privées existantes doivent être migrées vers le nouveau modèle de ressource pour pouvoir s’afficher dans la nouvelle expérience de portail. Consultez les instructions ci-après sur la migration vers un nouveau modèle de ressources.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Comment migrer mes zones DNS privées vers le nouveau modèle ?
Nous vous recommandons vivement de migrer vers le nouveau modèle de ressource dès que possible. Le modèle de ressources existant continuera à être pris en charge mais ne sera plus enrichi de nouvelles fonctionnalités. À l’avenir, nous avons l’intention de déconseiller son utilisation en faveur d’un nouveau modèle de ressources. Pour plus d’informations sur la migration de vos zones DNS privées existantes vers un nouveau modèle de ressources, reportez-vous au [guide de migration des zones privées Azure DNS](private-dns-migration-guide.md).

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur Azure Private DNS](private-dns-overview.md)