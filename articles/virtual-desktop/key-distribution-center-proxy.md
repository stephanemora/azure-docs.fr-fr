---
title: Configurer le proxy de centre de distribution de clés Kerberos pour Windows Virtual Desktop – Azure
description: Comment configurer un pool d’hôtes Windows Virtual Desktop pour utiliser un proxy de centre de distribution de clés Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219653"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Configurer un proxy de centre de distribution de clés Kerberos (préversion)

> [!IMPORTANT]
> Cette fonctionnalité est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service, c’est pourquoi nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article vous montre comment configurer un proxy de centre de distribution de clés (KDC) Kerberos (préversion) pour votre pool d’hôtes. Ce proxy permet aux organisations de s’authentifier auprès de Kerberos en dehors des frontières de leur entreprise. Par exemple, vous pouvez utiliser le proxy de KDC pour activer l’authentification par carte à puce pour les clients externes.

## <a name="how-to-configure-the-kdc-proxy"></a>Comment configurer le proxy de KDC

Pour configurer le proxy de KDC :

1. Connectez-vous au portail Azure en tant qu’administrateur.

2. Accédez à la page Windows Virtual Desktop.

3. Sélectionnez le pool d’hôtes pour lequel vous souhaitez activer le proxy de KDC, puis sélectionnez **Propriétés RDP**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la page du portail Azure montrant un utilisateur sélectionnant les pools d’hôtes, le nom de l’exemple de pool d’hôtes, puis les propriétés RDP.](media/rdp-properties.png)

4. Sélectionnez l’onglet **Avancé**, puis entrez une valeur au format suivant sans espaces :

    > kdcproxyname:s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant l’onglet Avancé sélectionné, avec la valeur entrée comme décrit à l’étape 4.](media/advanced-tab-selected.png)

5. Sélectionnez **Enregistrer**.

6. Le pool d’hôtes sélectionné doit maintenant commencer à émettre des fichiers de connexion RDP avec le champ kdcproxyname que vous avez entré inclus.

## <a name="next-steps"></a>Étapes suivantes

Le rôle Passerelle des services Bureau à distance dans Services Bureau à distance comprend un service de proxy de KDC. Consultez [Déployer le rôle Passerelle des services Bureau à distance dans Windows Virtual Desktop](rd-gateway-role.md) pour savoir comment en définir un comme cible pour Windows Virtual Desktop.
