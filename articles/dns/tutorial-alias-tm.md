---
title: 'Tutoriel : Créer un enregistrement d’alias Azure DNS pour prendre en charge les noms d’apex de domaine avec Traffic Manager'
description: Ce tutoriel vous montre comment configurer un enregistrement d’alias Azure DNS pour prendre en charge l’utilisation du nom d’apex de votre domaine avec Traffic Manager.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 4a5d8968861f6f2938e605d2f7106529ca401df4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967436"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Tutoriel : configurer un enregistrement d’alias pour prendre en charge des noms de domaine d’apex avec Traffic Manager 

Vous pouvez créer un enregistrement d’alias pour l’apex de votre nom de domaine (par exemple contoso.com) pour référencer un profil Traffic Manager. Ainsi, au lieu d’utiliser pour cela un service de redirection, vous pouvez configurer Azure DNS pour référencer un profil Traffic Manager directement à partir de votre zone. 


Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une infrastructure de machines virtuelles hôtes et de réseau
> * Créer un profil Traffic Manager
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
2. En haut à gauche du portail, cliquez sur **Créer une ressource**, tapez *groupe de ressources* dans la zone de recherche et créez un groupe de ressources nommé **RG-DNS-Alias-TM**.
3. Cliquez sur **Créer une ressource**, cliquez sur **Mise en réseau**, puis cliquez sur **Réseau virtuel**.
4. Créez un réseau virtuel nommé **VNet-Servers**, placez-le dans le groupe de ressources **RG-DNS-Alias-TM** et nommez le sous-réseau **SN-Web**.

## <a name="create-two-web-server-virtual-machines"></a>Créer deux machines virtuelles de serveur web
1. Cliquez sur **Créer une ressource**, puis cliquez sur **Machine virtuelle Windows Server 2016**.
2. Tapez **Web-01** pour le nom et placez la machine virtuelle dans le groupe de ressources **RG-DNS-Alias-TM**. Tapez un nom d’utilisateur et un mot de passe, puis cliquez sur **OK**.
3. Pour **Taille**, choisissez une référence (SKU) avec 8 Go de RAM.
4. Pour **Paramètres**, sélectionnez le réseau virtuel **VNet-Servers** et le sous-réseau **SN-Web**.
5. Cliquez sur **Adresse IP publique** puis, sous **Affectation**, cliquez sur **Statique**, puis cliquez sur **OK**.
6. Pour les ports d’entrée publics, sélectionnez **HTTP**, **HTTPS** et **RDP (3389)**, puis cliquez sur **OK**.
7. Sur la page Résumé, cliquez sur **Créer**.

   Ceci prend quelques minutes.
6. Répétez cette procédure pour créer une autre machine virtuelle nommée **Web-02**.

### <a name="add-a-dns-label"></a>Ajouter une étiquette DNS
Les adresses IP publiques nécessitent une étiquette DNS pour fonctionner avec Traffic Manager.
1. Dans le groupe de ressources **RG-DNS-Alias-TM**, cliquez sur l’adresse IP publique **Web-01-ip**.
2. Sous **Paramètres**, cliquez sur **Configuration**.
3. Dans la zone de texte Étiquette du nom DNS, tapez **web01pip**.
4. Cliquez sur **Enregistrer**.

Répétez cette procédure pour l’adresse IP publique **Web-02-ip** en utilisant **web02pip** pour l’étiquette du nom DNS.

### <a name="install-iis"></a>Installer IIS

Installez IIS sur **Web-01** et sur **Web-02**.

1. Connectez-vous à **Web-01**.
2. Dans le tableau de bord Gestionnaire de serveur, cliquez sur **Ajouter des rôles et fonctionnalités**.
3. Cliquez trois fois de suite sur **Suivant** et, dans la page **Rôles de serveur**, cliquez sur **Serveur Web (IIS)**.
4. Cliquez sur **Ajouter des fonctionnalités**, puis cliquez sur **Suivant**.
5. Cliquez sur **Suivant** quatre fois de suite, puis sur **Installer**.

   L’exécution de cette opération nécessite quelques minutes.
6. Une fois l’installation terminée, cliquez sur **Fermer**.
7. Ouvrez un navigateur web et accédez à **localhost** pour vérifier que la page web IIS par défaut s’affiche.

Répétez ce processus pour installer IIS sur **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager

Due 

1. Ouvrez le groupe de ressources **RG-DNS-Alias-TM**, puis cliquez sur l’adresse IP publique **Web-01-ip**. Notez l’adresse IP pour une utilisation ultérieure. Répétez ces étapes pour l’adresse IP publique **Web-02-ip**.
1. Cliquez sur **Créer une ressource**, cliquez sur **Mise en réseau**, puis cliquez sur **Profil Traffic Manager**.
2. Pour le nom, tapez **TM-alias-test** et placez-le dans le groupe de ressources **RG-DNS-Alias-TM**.
3. Cliquez sur **Créer**.
4. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**.
5. Dans la page Profil Traffic Manager, sous **Paramètres**, cliquez sur **Points de terminaison**.
6. Cliquez sur **Add**.
7. Pour **Type**, sélectionnez **Point de terminaison externe** et pour **Nom** tapez **EP-Web01**.
8. Dans la zone de texte **Nom de domaine complet (FQDN) ou adresse IP**, tapez l’adresse IP de **Web-01-ip** que vous avez notée précédemment.
9. Sélectionnez le même **Emplacement** que vos autres ressources, puis cliquez sur **OK**.

Répétez cette procédure pour ajouter le point de terminaison **Web-02** en utilisant l’adresse IP que vous avez notée précédemment pour **Web-02-ip**.

## <a name="create-an-alias-record"></a>Créer un enregistrement d’alias

Créez un enregistrement d’alias qui pointe vers le profil Traffic Manager.

1. Cliquez sur votre zone Azure DNS pour ouvrir la zone.
2. Cliquez sur **Jeu d’enregistrements**.
3. Laissez la zone de texte **Nom** vide pour représenter l’apex du nom de domaine (par exemple contoso.com).
4. Laissez le **Type** défini sur un enregistrement **A**.
5. Cliquez sur la case à cocher **Alias du jeu d’enregistrements**.
6. Cliquez sur **Choisir un service Azure**, puis sélectionnez le profil Traffic Manager **TM-alias-test**.

## <a name="test-the-alias-record"></a>Tester l’enregistrement d’alias

1. À partir d’un navigateur web, accédez à l’apex de votre nom de domaine (par exemple contoso.com). Vous devez voir la page web d’IIS par défaut. Fermez le navigateur web.
2. Arrêtez la machine virtuelle **Web-01** et attendez quelques minutes pour qu’elle soit complètement arrêtée.
3. Ouvrez un nouveau navigateur web et accédez à nouveau à l’apex de votre nom de domaine.
4. Vous devez à nouveau voir la page d’IIS par défaut, car Traffic Manager a géré la situation et dirigé le trafic vers **Web-02**.

## <a name="clean-up-resources"></a>Supprimer les ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources **RG-DNS-Alias-TM** pour supprimer toutes les ressources créées dans ce tutoriel.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un enregistrement d’alias afin d’utiliser l’apex de votre nom de domaine pour référencer un profil Traffic Manager. Pour en savoir plus sur Azure DNS et les applications web, passez au tutoriel sur les applications web.

> [!div class="nextstepaction"]
> [Créer des enregistrements DNS pour une application web dans un domaine personnalisé](./dns-web-sites-custom-domain.md)
