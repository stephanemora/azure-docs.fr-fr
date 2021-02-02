---
title: Configurer le proxy de centre de distribution de clés Kerberos pour Windows Virtual Desktop – Azure
description: Comment configurer un pool d’hôtes Windows Virtual Desktop pour utiliser un proxy de centre de distribution de clés Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 01/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f123659941eaeb3b8ceeb6a999abf836eb9cf5ea
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797998"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>Configurer un proxy de centre de distribution de clés Kerberos

Cet article vous montre comment configurer un proxy de centre de distribution de clés (KDC) Kerberos pour votre pool d’hôtes. Ce proxy permet aux organisations de s’authentifier auprès de Kerberos en dehors des frontières de leur entreprise. Par exemple, vous pouvez utiliser le proxy de KDC pour activer l’authentification par carte à puce pour les clients externes.

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
