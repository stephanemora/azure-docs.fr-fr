---
title: Azure VMware Solutions (AVS) – Configurer DNS pour un cloud privé AVS
description: Décrit comment configurer la résolution de noms DNS pour l’accès au serveur vCenter sur un cloud privé AVS à partir de stations de travail locales
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 12c4362ae1b075af132d5971f4fe0461c9d91733
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083085"
---
# <a name="configure-dns-for-name-resolution-for-avs-private-cloud-vcenter-access-from-on-premises-workstations"></a>Configurer DNS pour la résolution de noms pour l’accès à un vCenter de cloud privé AVS à partir de stations de travail locales

Pour accéder au serveur vCenter sur un cloud privé AVS à partir de stations de travail locales, vous devez configurer la résolution d’adresses DNS afin que le serveur vCenter puisse être désigné par nom d’hôte ainsi que par adresse IP.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-avs-private-cloud"></a>Obtenir l’adresse IP du serveur DNS pour votre cloud privé AVS

1. Connectez-vous au portail [AVS](access-cloudsimple-portal.md).

2. Accédez à **Ressources** > **Clouds privés AVS**, puis sélectionnez le cloud privé AVS auquel vous souhaitez vous connecter.

3. Sur la page **Résumé** du cloud privé AVS, sous **Informations de base**, copiez l’adresse IP du serveur DNS du cloud privé AVS.

    ![Serveurs DNS de cloud privé AVS](media/private-cloud-dns-server.png)


Utilisez une de ces options pour la configuration DNS.

* [Créer une zone sur le serveur DNS pour *.cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Créer un redirecteur conditionnel sur votre serveur DNS local pour résoudre *.cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Créer une zone sur le serveur DNS pour *.cloudsimple.io

Vous pouvez configurer une zone en tant que zone de stub et pointer vers les serveurs DNS sur le cloud privé pour la résolution de noms. Cette section fournit des informations sur l’utilisation d’un serveur DNS BIND ou d’un serveur DNS Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Créer une zone sur un serveur DNS BIND

Le fichier et les paramètres spécifiques à configurer peuvent varier en fonction de votre configuration DNS individuelle.

Par exemple, pour la configuration de serveur BIND par défaut, modifiez le fichier /etc/named.conf sur votre serveur DNS et ajoutez les informations de zone suivantes.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Créer une zone sur un serveur DNS Microsoft Windows

1. Cliquez avec le bouton droit sur le serveur DNS et sélectionnez **Nouvelle zone**. 
  
    ![Nouvelle zone](media/DNS01.png)
2. Sélectionnez **Zone de stub**, puis cliquez sur **Suivant**.

    ![Nouvelle zone](media/DNS02.png)
3. Sélectionnez l’option appropriée en fonction de votre environnement, puis cliquez sur **Suivant**.

    ![Nouvelle zone](media/DNS03.png)
4. Sélectionnez **Zone de recherche directe**, puis cliquez sur **Suivant**.

    ![Nouvelle zone](media/DNS01.png)
5. Entrez le nom de la zone, puis cliquez sur **Suivant**.

    ![Nouvelle zone](media/DNS05.png)
6. Entrez les adresses IP des serveurs DNS de votre cloud privé AVS, que vous avez obtenues à partir du portail AVS.

    ![Nouvelle zone](media/DNS06.png)
7. Cliquez sur **Suivant** si nécessaire pour terminer l’installation de l’assistant.

## <a name="create-a-conditional-forwarder"></a>Créer un redirecteur conditionnel

Un redirecteur conditionnel transfère toutes les requêtes de résolution de noms DNS au serveur désigné. Avec cette configuration, toute requête pour *.cloudsimple.io est transférée aux serveurs DNS situés sur le cloud privé AVS. Les exemples suivants montrent comment configurer des redirecteurs sur différents types de serveurs DNS.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Créer un redirecteur conditionnel sur un serveur DNS BIND

Le fichier et les paramètres spécifiques à configurer peuvent varier en fonction de votre configuration DNS individuelle.

Par exemple, pour la configuration de serveur BIND par défaut, modifiez le fichier /etc/named.conf sur votre serveur DNS et ajoutez les informations de redirection conditionnelle suivantes.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Créer un redirecteur conditionnel sur un serveur DNS Microsoft Windows

1. Ouvrez le Gestionnaire DNS sur le serveur DNS.
2. Cliquez avec le bouton droit sur **Redirecteurs conditionnels** et sélectionnez l’option permettant d’ajouter un nouveau redirecteur conditionnel.

    ![Redirecteur conditionnel 1 DNS Windows](media/DNS08.png)
3. Entrez le domaine DNS et l’adresse IP des serveurs DNS dans le cloud privé AVS, puis cliquez sur **OK**.
