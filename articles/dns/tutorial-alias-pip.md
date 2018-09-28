---
title: 'Tutoriel : Créer un enregistrement d’alias Azure DNS pour référencer une adresse IP publique Azure'
description: Ce tutoriel vous montre comment créer un enregistrement d’alias Azure DNS pour référencer une adresse IP publique Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 842015d853790e3ac78214cca6a70becb7f9fadf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991219"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Tutoriel : Configurer un enregistrement d’alias pour référencer une adresse IP publique Azure 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une infrastructure réseau
> * Créez une machine virtuelle de serveur web
> * Créer un enregistrement d’alias
> * Tester l’enregistrement d’alias


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis
Vous devez disposer d’un nom de domaine disponible, que vous pouvez héberger dans Azure DNS pour le test. Vous devez disposer d’un contrôle total de ce domaine, notamment de la possibilité de définir les enregistrements de serveur de noms pour le domaine.

Pour obtenir des instructions pour héberger votre domaine dans Azure DNS, consultez [Tutoriel : Héberger votre domaine dans Azure DNS](dns-delegate-domain-azure-dns.md).

L’exemple de domaine utilisé pour ce tutoriel est contoso.com, mais vous devez utiliser votre propre nom de domaine.

## <a name="create-the-network-infrastructure"></a>Créer l’infrastructure réseau
Créez d’abord un réseau virtuel et un sous-réseau pour y placer vos serveurs web.
1. Connectez-vous au portail Azure sur http://portal.azure.com.
2. En haut à gauche du portail, cliquez sur **Créer une ressource**, tapez *groupe de ressources* dans la zone de recherche et créez un groupe de ressources nommé **RG-DNS-Alias-pip**.
3. Cliquez sur **Créer une ressource**, cliquez sur **Mise en réseau**, puis cliquez sur **Réseau virtuel**.
4. Créez un réseau virtuel nommé **VNet-Servers**, placez-le dans le groupe de ressources **RG-DNS-Alias-pip** et nommez le sous-réseau **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Créez une machine virtuelle de serveur web
1. Cliquez sur **Créer une ressource**, puis cliquez sur **Machine virtuelle Windows Server 2016**.
2. Tapez **Web-01** pour le nom et placez la machine virtuelle dans le groupe de ressources **RG-DNS-Alias-TM**. Tapez un nom d’utilisateur et un mot de passe, puis cliquez sur **OK**.
3. Pour **Taille**, choisissez une référence (SKU) avec 8 Go de RAM.
4. Pour **Paramètres**, sélectionnez le réseau virtuel **VNet-Servers** et le sous-réseau **SN-Web**. Pour les ports d’entrée publics, sélectionnez **HTTP**, **HTTPS** et **RDP (3389)**, puis cliquez sur **OK**.
5. Sur la page Résumé, cliquez sur **Créer**.

   Ceci prend quelques minutes.

### <a name="install-iis"></a>Installer IIS

Installez IIS sur **Web-01**.

1. Connectez-vous à **Web-01**.
2. Dans le tableau de bord Gestionnaire de serveur, cliquez sur **Ajouter des rôles et fonctionnalités**.
3. Cliquez trois fois de suite sur **Suivant** et, dans la page **Rôles de serveur**, cliquez sur **Serveur Web (IIS)**.
4. Cliquez sur **Ajouter des fonctionnalités**, puis cliquez sur **Suivant**.
5. Cliquez sur **Suivant** quatre fois de suite, puis sur **Installer**.

   L’exécution de cette opération nécessite quelques minutes.
6. Une fois l’installation terminée, cliquez sur **Fermer**.
7. Ouvrez un navigateur web et accédez à **localhost** pour vérifier que la page web IIS par défaut s’affiche.

## <a name="create-an-alias-record"></a>Créer un enregistrement d’alias

Créez un enregistrement d’alias qui pointe vers l’adresse IP publique.

1. Cliquez sur votre zone Azure DNS pour ouvrir la zone.
2. Cliquez sur **Jeu d’enregistrements**.
3. Dans la zone de texte **Nom**, entrez **web01**.
4. Laissez le **Type** défini sur un enregistrement **A**.
5. Cliquez sur la case à cocher **Alias du jeu d’enregistrements**.
6. Cliquez sur **Choisir un service Azure** et sélectionnez l’adresse IP publique **Web-01-ip**.

## <a name="test-the-alias-record"></a>Tester l’enregistrement d’alias

1. Dans le groupe de ressources **RG-DNS-Alias-pip**, cliquez sur la machine virtuelle **Web-01**. Notez l’adresse IP publique.
1. À partir d’un navigateur web, accédez au nom de domaine complet pour la machine virtuelle Web01-01. Par exemple, **web01.contoso.com**. Vous devez voir la page web d’IIS par défaut.
2. Fermez le navigateur web.
3. Arrêtez la machine virtuelle **Web-01**, puis redémarrez-la.
4. Quand elle redémarre, notez la nouvelle adresse IP publique pour la machine virtuelle.
5. Ouvrez un navigateur web et accédez à nouveau au nom de domaine complet pour la machine virtuelle Web01-01. Par exemple, **web01.contoso.com**.
6. Cette opération doit à nouveau réussir, car vous avez utilisé un enregistrement d’alias pour pointer vers la ressource d’adresse IP publique, et non pas un enregistrement A standard.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources **RG-DNS-Alias-pip** pour supprimer toutes les ressources créées dans ce tutoriel.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un enregistrement d’alias pour référencer une adresse IP publique Azure. Pour en savoir plus sur Azure DNS et les applications web, passez au tutoriel sur les applications web.

> [!div class="nextstepaction"]
> [Créer des enregistrements DNS pour une application web dans un domaine personnalisé](./dns-web-sites-custom-domain.md)
