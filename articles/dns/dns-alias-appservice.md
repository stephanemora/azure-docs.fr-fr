---
title: Héberger des applications web Azure à charge équilibrée à l’extrémité de la zone
description: Utilisez un enregistrement d’alias Azure DNS pour héberger des applications web à charge équilibrée à l’extrémité de la zone.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 11/3/2018
ms.author: victorh
ms.openlocfilehash: b08eae072c2fbe420401424baf97a25b4cbbe87b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086324"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Héberger des applications web Azure à charge équilibrée à l’extrémité de la zone

Le protocole DNS empêche l’affectation de tout enregistrement autre qu’un enregistrement A ou AAAA au niveau de l’apex de zone. contoso.com est un exemple d’extrémité de zone. Cette restriction pose un problème pour les propriétaires d’application qui ont des applications à charge équilibrée derrière un profil Traffic Manager. Il est impossible de pointer vers le profil Traffic Manager à partir de l’enregistrement d’apex de zone. Les propriétaires d’application doivent donc trouver une solution de contournement. Une redirection au niveau de la couche Application doit rediriger vers un autre domaine à partir de l’apex de zone. Par exemple, une redirection à partir de contoso.com pour www\.contoso.com. Cette disposition présente un point de défaillance unique de la fonction de redirection.

Les enregistrements d’alias éliminent ce problème. Les propriétaires d’applications peuvent maintenant pointer leur enregistrement d’apex de zone vers un profil Traffic Manager disposant de points de terminaison externes. Les propriétaires d’applications peuvent pointer vers le même profil Traffic Manager que celui utilisé pour tout autre domaine dans leur zone DNS.

Par exemple, contoso.com et www\.contoso.com peut pointer vers le même profil Traffic Manager. C’est le cas tant que le profil Traffic Manager ne compte que des points de terminaison externes.

Cet article explique comment créer un enregistrement d’alias pour une extrémité de domaine et configurer des points de terminaison de profil Traffic Manager pour des applications web.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

Vous devez disposer d’un nom de domaine disponible, que vous pouvez héberger dans Azure DNS pour le test. Vous devez disposer d’un contrôle total de ce domaine. Le contrôle total comprend notamment la possibilité de définir les enregistrements de serveur de noms pour le domaine.

Pour obtenir des instructions héberger votre domaine dans Azure DNS, consultez [didacticiel : Héberger votre domaine dans Azure DNS](dns-delegate-domain-azure-dns.md).

L’exemple de domaine utilisé pour ce tutoriel est contoso.com, mais utilisez votre propre nom de domaine.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources qui contiendra toutes les ressources utilisées dans cet article.

## <a name="create-app-service-plans"></a>Créer des plans App Service

Créez deux plans Web App Service dans votre groupe de ressources en suivant les informations de configuration du tableau ci-dessous. Pour plus d’informations sur la création d’un plan App Service, voir [Gérer un plan App Service dans Azure](../app-service/app-service-plan-manage.md).


|Nom  |Système d’exploitation  |Lieu  |Niveau de tarification  |
|---------|---------|---------|---------|
|ASP-01     |Windows|USA Est|Dev/Test D1-Shared|
|ASP-02     |Windows|USA Centre|Dev/Test D1-Shared|

## <a name="create-app-services"></a>Créer des applications App Service

Créez deux applications web, une dans chaque plan App Service.

1. En haut à gauche de la page du Portail Azure, sélectionnez **Créer une ressource**.
2. Tapez **Application web** dans la barre de recherche et appuyez sur Entrée.
3. Cliquez sur **Application web**.
4. Cliquez sur **Créer**.
5. Acceptez les valeurs par défaut et utilisez le tableau suivant pour configurer les deux applications web :

   |Nom<br>(doit être unique sur . azurewebsites.net)|Groupe de ressources |Plan App Service/Emplacement
   |---------|---------|---------|
   |App-01|Utilisez l’existant<br>Sélectionnez votre groupe de ressources|ASP-01(USA Est)|
   |App-02|Utilisez l’existant<br>Sélectionnez votre groupe de ressources|ASP-02(USA Centre)|

### <a name="gather-some-details"></a>Collecter quelques informations

Notez maintenant l’adresse IP et le nom d’hôte des applications.

1. Ouvrez votre groupe de ressources et cliquez sur votre première application (**App-01** dans cet exemple).
2. Dans la colonne de gauche, cliquez sur **Propriétés**.
3. Notez l’adresse sous **URL**, puis, sous **Adresses IP sortantes**, notez la première adresse IP de la liste. Vous utiliserez ces informations au moment de configurer vos points de terminaison Traffic Manager.
4. Répétez l’opération pour **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager

Créez un profil Traffic Manager dans votre groupe de ressources. Utilisez les valeurs par défaut et tapez un nom unique sur l’espace de noms trafficmanager.net.

Pour plus d’informations sur la création d’un profil Traffic Manager, consultez [Guide de démarrage rapide : Créer un profil Traffic Manager pour une application web à haute disponibilité](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Créer des points de terminaison

Vous pouvez maintenant créer les points de terminaison pour les deux applications web.

1. Ouvrez votre groupe de ressources, puis cliquez sur votre profil Traffic Manager.
2. Dans la colonne de gauche, cliquez sur **Points de terminaison**.
3. Cliquez sur **Add**.
4. Aidez-vous du tableau suivant pour configurer les points de terminaison :

   |Type  |Nom  |Cible  |Lieu  |Paramètres d’en-tête personnalisé|
   |---------|---------|---------|---------|---------|
   |Point de terminaison externe     |End-01|Adresse IP enregistrée pour App-01|USA Est|host:\<URL enregistrée pour App-01\><br>Exemple : **host:app-01.azurewebsites.net**|
   |Point de terminaison externe     |End-02|Adresse IP enregistrée pour App-02|USA Centre|host:\<URL enregistrée pour App-02\><br>Exemple : **host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Créer une zone DNS

Vous pouvez utiliser une zone DNS existante à des fins de test, ou en créer une nouvelle. Pour créer et déléguer une zone DNS dans Azure, consultez [didacticiel : Héberger votre domaine dans Azure DNS](dns-delegate-domain-azure-dns.md).

### <a name="add-the-alias-record-set"></a>Ajouter le jeu d’enregistrements d’alias

Lorsque votre zone DNS est prête, vous pouvez ajouter un enregistrement d’alias pour l’extrémité de la zone.

1. Ouvrez votre groupe de ressources, puis cliquez sur la zone DNS.
2. Cliquez sur **Jeu d’enregistrements**.
3. Ajoutez le jeu d’enregistrements suivant le tableau ci-dessous :

   |Nom  |Type  |Jeu d’enregistrements d’alias  |Type d’alias  |Ressource Azure|
   |---------|---------|---------|---------|-----|
   |@     |A|Oui|Ressource Azure|Traffic Manager – votre profil|

## <a name="add-custom-hostnames"></a>Ajouter des noms d’hôte personnalisés

Ajoutez un nom d’hôte personnalisé aux deux applications web.

1. Ouvrez votre groupe de ressources et cliquez sur votre première application web.
2. Dans la colonne de gauche, cliquez sur **Domaines personnalisés**.
3. Cliquez sur **Ajouter un nom d’hôte**.
4. Sous Nom d’hôte, tapez votre nom de domaine. Par exemple, contoso.com.

   Votre domaine doit réussir la validation et présenter des coches vertes à côté de **disponibilité du nom d’hôte** et de **propriété du domaine**.
5. Cliquez sur **Ajouter un nom d’hôte**.
6. Pour voir le nouveau nom d’hôte sous **Noms d’hôte affectés au site**, actualisez votre navigateur. Les modifications n’apparaissent pas forcément tout de suite.
7. Répétez cette procédure pour votre deuxième application web.

## <a name="test-your-web-apps"></a>Tester les applications web

Vous pouvez maintenant passer aux tests, pour vérifier que vous pouvez atteindre votre application web et qu’elle est à charge équilibrée.

1. Ouvrez un navigateur web et accédez à votre domaine. Par exemple, contoso.com. La page de l’application web par défaut devrait apparaître.
2. Arrêtez votre première application web.
3. Fermez votre navigateur web, puis attendez quelques minutes.
4. Démarrez votre navigateur web et accédez à votre domaine. Vous devriez toujours voir la page de l’application web par défaut.
5. Arrêtez votre deuxième application web.
6. Fermez votre navigateur web, puis attendez quelques minutes.
7. Démarrez votre navigateur web et accédez à votre domaine. L’erreur 403 devrait apparaître, indiquant que l’application web est arrêtée.
8. Démarrez votre deuxième application web.
9. Fermez votre navigateur web, puis attendez quelques minutes.
10. Démarrez votre navigateur web et accédez à votre domaine. La page de l’application web par défaut devrait réapparaître.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les enregistrements d’alias, consultez les articles suivant :

- [Tutoriel : Configurer un enregistrement d'alias pour faire référence à une adresse IP publique](tutorial-alias-pip.md)
- [Tutoriel : Configurer un enregistrement d'alias pour prendre en charge des noms de domaine d'apex avec Traffic Manager](tutorial-alias-tm.md)
- [FAQ DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
