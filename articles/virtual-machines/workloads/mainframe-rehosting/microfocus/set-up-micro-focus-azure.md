---
title: Installation de Micro Focus Enterprise Server 4.0 et code Enterprise Developer 4.0 sur Azure | Microsoft Docs
description: Ré-héberger votre IBM z/OS mainframe les charges de travail à l’aide de l’évolution de Micro Focus et tester l’environnement sur les machines virtuelles (VM) Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 45d6f8606c665d78783f987c2f2b49a77801639c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304604"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Installation de Micro Focus Enterprise Server 4.0 et code Enterprise Developer 4.0 sur Azure

Cet article explique comment configurer [Micro Focus Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) et [Micro Focus Enterprise Developer 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) sur Azure.

Une charge de travail courantes sur Azure est un environnement de développement et de test. Ce scénario est courant, car il est par conséquent, économique et facile à déployer et à détruire. Avec le serveur d’entreprise, Micro Focus a créé une des plus grandes mainframe réhébergement plateformes disponibles. Vous pouvez exécuter des charges de travail z/OS sur un x86 moins coûteux plateforme sur Azure à l’aide de Windows ou Linux machines virtuelles (VM).

Cette configuration utilise des machines virtuelles Azure exécutant l’image Windows Server 2016 à partir de la place de marché Azure avec Microsoft SQL Server 2017 est déjà installé. Cette configuration s’applique également à Azure Stack.

L’environnement de développement correspondant pour le serveur d’entreprise est développeur en entreprise, qui s’exécute sur soit Microsoft Visual Studio 2017 ou version ultérieure, Visual Studio Community (gratuite à télécharger), ou Eclipse. Cet article explique comment le déployer à l’aide d’une machine virtuelle de Windows Server 2016 qui est fourni avec Visual Studio 2017 ou version ultérieure.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, consultez ces conditions préalables :

- Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Le logiciel de Micro Focus et une licence valide (ou licence d’essai). Si vous êtes un client de Micro Focus existant, contactez votre représentant de Micro Focus. Sinon, [demandez un essai](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Obtenir la documentation de [Enterprise Server et code Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Une bonne pratique consiste à configurer un tunnel de réseau privé virtuel (VPN) site à site ou un serveur de rebond, donc vous pouvez contrôler l’accès aux machines virtuelles Azure.

## <a name="install-enterprise-server"></a>Installer Enterprise Server

1. Pour une meilleure sécurité et la facilité de gestion, envisagez de créer un nouveau groupe de ressources uniquement pour ce projet, par exemple, **RGMicroFocusEntServer**. Utilisez la première partie du nom dans Azure à choisir le type de ressource pour le rendre plus facile à repérer dans une liste.

2. Création d’une machine virtuelle À partir de la place de marché Azure, sélectionnez la machine virtuelle et le système d’exploitation souhaité. Voici une configuration recommandée :

    - **Enterprise Server**: Sélectionnez machine virtuelle les v3 ES2 (avec 2 processeurs virtuels et 16 Go de mémoire) avec Windows Server 2016 et SQL Server 2017 est installé. Cette image est disponible à partir de la place de marché Azure. Enterprise Server pouvez utiliser la base de données SQL Azure également.

    - **Enterprise Developer**: Sélectionnez la machine virtuelle B2ms (avec 2 processeurs virtuels et 8 Go de mémoire) avec Windows 10 et Visual Studio est installé. Cette image est disponible à partir de la place de marché Azure.

3. Dans le **notions de base** section, entrez votre nom d’utilisateur et le mot de passe. Sélectionnez le **abonnement** et **région / l’emplacement** vous souhaitez utiliser pour les machines virtuelles. Sélectionnez **RGMicroFocusEntServer** pour le groupe de ressources.

4. Placez les deux machines virtuelles dans le même réseau virtuel afin qu’ils peuvent communiquer entre eux.

5. Acceptez les valeurs par défaut pour le reste des paramètres. N’oubliez pas le nom d’utilisateur et le mot de passe que vous créez pour l’administrateur de ces machines virtuelles.

6. Lorsque les machines virtuelles ont été créés, ouvrir des ports entrants 9003, 86 et 80 pour HTTP et 3389 pour RDP sur l’ordinateur du serveur d’entreprise et 3389 sur l’ordinateur du développeur.

7. Pour vous connecter à la machine virtuelle de serveur d’entreprise, dans le portail Azure, sélectionnez la machine virtuelle v3 de ES2. Accédez à la **vue d’ensemble** section et sélectionnez **Connect** pour lancer une session RDP. Connectez-vous avec les informations d’identification que vous avez créé pour la machine virtuelle.

8. À partir de la session RDP, chargez les deux fichiers suivants. Étant donné que vous utilisez Windows, vous pouvez glisser -déplacer les fichiers dans la session RDP :

    - **es\_40. exe**, le fichier d’installation de serveur d’entreprise.

    - **mflic**, le fichier de licence correspondant, sans qu’il ne charge pas Enterprise Server.

9. Double-cliquez sur le fichier pour démarrer l’installation. Dans la première fenêtre, sélectionnez l’emplacement d’installation et acceptez le contrat de licence utilisateur final.

     ![Micro Focus Enterprise Server écran](media/01-enterprise-server.png)

     Lorsque le programme d’installation est terminée, le message suivant apparaît :

     ![Micro Focus Enterprise Server écran](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Rechercher des mises à jour

Après l’installation, veillez à consulter les mises à jour supplémentaires depuis un nombre de conditions préalables telles que le package redistribuable Microsoft C++ et .NET Framework sont installés en même temps que le serveur d’entreprise.

### <a name="upload-the-license"></a>Télécharger la licence

1. Démarrez l’Administration de licence de Micro Focus.

2. Cliquez sur **Démarrer** \> **Micro Focus License Manager** \> **License Administration**, puis cliquez sur le **installer** onglet. Choisissez le type de format de licence à télécharger : un fichier de licence ou d’un code de licence de 16 caractères. Par exemple, pour un fichier, dans **fichier de licence**, accédez à la **mflic** fichiers téléchargés précédemment sur la machine virtuelle et sélectionnez **installer les licences**.

     ![Boîte de dialogue Micro Focus License Administration](media/03-enterprise-server.png)

3. Vérifiez que le serveur d’entreprise chargée. Essayez de lancer le site d’Administration de serveur d’entreprise à partir d’un navigateur à l’aide de cette URL <http://localhost:86/> . La page d’Administration de serveur d’entreprise s’affiche comme illustré.

     ![Page d’Administration de serveur d’entreprise](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Installer Enterprise Developer sur l’ordinateur du développeur

1. Sélectionnez le groupe de ressources créé précédemment (par exemple, **RGMicroFocusEntServer**), puis sélectionnez l’image de développeur.

2. Pour vous connecter à la machine virtuelle, accédez à la **vue d’ensemble** section et sélectionnez **Connect**. Cette connexion lance une session RDP. Connectez-vous avec les informations d’identification que vous avez créé pour la machine virtuelle.

3. À partir de la session RDP, chargez les deux fichiers suivants (glisser -déplacer si vous le souhaitez) :

    - **edvs2017.exe**, le fichier d’installation de serveur d’entreprise.

    - **mflic**, le fichier de licence correspondant (Enterprise Developer ne chargera pas sans lui).

4. Double-cliquez sur le **edvs2017.exe** fichier pour démarrer l’installation. Dans la première fenêtre, sélectionnez l’emplacement d’installation et acceptez le contrat de licence utilisateur final. Si vous le souhaitez, choisissez **9.5 de Rumba installer** pour installer cet émulateur de terminal, vous aurez probablement besoin.

     ![Micro Focus Enterprise Developer pour la boîte de dialogue d’installation de Visual Studio 2017](media/04-enterprise-server.png)

5. Une fois l’installation terminée, le message suivant apparaît :

     ![Message de réussite de l’installation](media/05-enterprise-server.png)

6. Démarrez le Gestionnaire de licences de Micro Focus comme vous le faisiez pour Enterprise Server. Choisissez **Démarrer** \> **Micro Focus License Manager** \> **Administration de licence**, puis cliquez sur le **installer**onglet.

7. Choisissez le type de format de licence à télécharger : un fichier de licence ou d’un code de licence de 16 caractères. Par exemple, pour un fichier, dans **fichier de licence**, accédez à la **mflic** fichiers téléchargés précédemment sur la machine virtuelle et sélectionnez **installer les licences**.

     ![Boîte de dialogue Micro Focus License Administration](media/07-enterprise-server.png)

Lors du chargement de développeur en entreprise, votre déploiement d’un environnement de développement et de test de Micro Focus sur Azure est terminé !

## <a name="next-steps"></a>Étapes suivantes

- [Configurer l’application de démonstration de la banque](./demo.md)
- [Exécuter le serveur d’entreprise dans des conteneurs Docker](./run-enterprise-server-container.md)
- [Migration d’applications mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
