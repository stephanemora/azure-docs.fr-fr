---
title: Certificats requis pour les principaux de mise en liste verte dans Azure Application Gateway
description: Cet article fournit des exemples de la façon dont un certificat SSL peut être converti vers le certificat d’authentification et le certificat racine approuvé qui sont nécessaires pour la mise en liste verte des instances de serveur principal dans Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/14/2019
ms.author: absha
ms.openlocfilehash: 72ee9123ad959c0c7240d4f7a906adc1a4dd1a93
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260498"
---
# <a name="create-certificates-for-whitelisting-backend-with-azure-application-gateway"></a>Créer des certificats pour le serveur principal de mise en liste verte avec Azure Application Gateway

Pour effectuer le SSL de bout en bout, application gateway requiert les instances de serveur principal à débloquer en chargeant des certificats racines approuvés / d’authentification. En cas de référence (SKU) de v1, les certificats d’authentification sont requis tandis qu’en cas de référence (SKU) de v2, approuvé les certificats racine sont requis pour la mise en liste verte les certificats

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> - Exporter le certificat d’authentification à partir d’un certificat de serveur principal (pour référence (SKU) de v1)
> - Exporter le certificat racine approuvé à partir d’un certificat de serveur principal (pour la référence SKU v2)

## <a name="prerequisites"></a>Conditions préalables

Vous avez besoin d’un certificat de serveur principal existant pour générer les certificats d’authentification ou les certificats racines de confiance requis pour la mise en liste verte des instances de serveur principal avec la passerelle d’application. Le certificat de serveur principal peut être identique au certificat SSL ou différents pour renforcer la sécurité. Passerelle d’application ne fournit aucun mécanisme pour créer ou acheter un certificat SSL. À des fins de test, vous pouvez créer un certificat auto-signé, mais vous ne devez pas l’utiliser pour les charges de travail de production. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Exporter le certificat d’authentification (pour référence (SKU) de v1)

Certificat d’authentification est nécessaire pour les instances de serveur principal de liste verte dans v1 de passerelle d’application référence (SKU). Certificat d’authentification est la clé publique des certificats de serveur principal en Base-64 codé X.509 (. Format CER). Dans cet exemple, nous utilise un certificat SSL pour le certificat de serveur principal et exporter sa clé publique à utiliser comme la certification de l’authentification. En outre, dans cet exemple, nous allons utiliser l’outil Gestionnaire de certificats Windows pour exporter les certificats requis. Vous pouvez choisir d’utiliser n’importe quel autre outil conformément à votre convenance.

À partir de votre certificat SSL, exportez le fichier .cer de clé publique (et non la clé privée). Les étapes suivantes permettent d’exporter le fichier .cer X.509 encodé de fichier en Base 64 (. Format CER) pour votre certificat :

1. Pour obtenir un fichier .cer du certificat, ouvrez **Gérer les certificats utilisateur**. Localisez le certificat, généralement dans « Certificates - Current User\Personal\Certificates » et avec le bouton droit. Cliquez sur **Toutes les tâches**, puis cliquez sur **Exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**. Si vous ne trouvez pas le certificat sous Current User\Personal\Certificates, il est possible que vous ayez ouvert par erreur « Certificats – Ordinateur local » et non « Certificats – Utilisateur actuel ». Si vous voulez ouvrir le Gestionnaire de certificats dans le champ d’application utilisateur actuel en utilisant PowerShell, tapez *certmgr* dans la fenêtre de la console.

   ![Exportation](./media/certificates-for-backend-authentication/export.png)

2. Dans l’assistant, cliquez sur **Suivant**.

   ![Exportation du certificat](./media/certificates-for-backend-authentication/exportwizard.png)

3. Sélectionnez **Non, ne pas exporter la clé privée**, puis cliquez sur **Suivant**.

   ![N’exportez pas la clé privée](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Sur la page **Format de fichier d’exportation**, sélectionnez **Codé à base 64 X.509 (.cer).**, puis cliquez sur **Suivant**.

   ![Encodage en base 64](./media/certificates-for-backend-authentication/base64.png)

5. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.

   ![Parcourir](./media/certificates-for-backend-authentication/browse.png)

6. Cliquez sur **Terminer** pour exporter le certificat.

   ![Finish](./media/certificates-for-backend-authentication/finish.png)

7. Votre certificat est correctement exporté.

   ![Succès](./media/certificates-for-backend-authentication/success.png)

   Le certificat exporté ressemble à ceci :

   ![Exporté](./media/certificates-for-backend-authentication/exported.png)

8. Si vous ouvrez le certificat exporté à l’aide du Bloc-notes, vous verrez quelque chose de similaire à cet exemple. La section en bleu contient les informations qui sont téléchargées vers la passerelle d’application. Si vous ouvrez votre certificat avec le Bloc-notes et qu’il ne ressemble pas à celui de l’exemple, cela signifie généralement que vous ne l’avez pas exporté au format X.509 de base 64 (.cer). De plus, si vous voulez utiliser un autre éditeur de texte, notez que certains éditeurs peuvent ajouter une mise en forme non souhaitée en arrière-plan. Cela peut créer des problèmes quand vous chargez le texte de ce certificat sur Azure.

   ![Ouverture du fichier avec le Bloc-notes](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exporter le certificat racine approuvé (par référence (SKU) de v2)

Approuvé le certificat racine est nécessaire pour les instances de serveur principal de liste verte dans v2 de passerelle d’application référence (SKU). Le certificat racine est une Base-64 codé X.509 (. Certificat racine du format CER) parmi les certificats de serveur principal. Dans cet exemple, nous utilise un certificat SSL pour le certificat de serveur principal, exporter sa clé publique et puis exporter le certificat racine de l’autorité de certification approuvée à partir de la clé publique dans un format codé en base64 pour obtenir le certificat racine approuvé. 

Les étapes suivantes vous permettent d’exporter le fichier .cer pour votre certificat :

1. Utilisez les étapes mentionnées dans la section 1-9 **certificat d’authentification exportation d’un certificat de serveur principal (pour référence (SKU) de v1)** ci-dessus pour exporter la clé publique à partir de votre certificat de serveur principal.

2. Une fois la clé publique a été exportée, ouvrez le fichier.

   ![Certificat d’autorisation d’ouvrir](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![sur le certificat](./media/certificates-for-backend-authentication/general.png)

3. Déplacer vers le chemin d’accès de Certification pour consulter l’autorité de certification.

   ![Détails du certificat](./media/certificates-for-backend-authentication/certdetails.png)

4. Sélectionnez le certificat racine et cliquez sur **afficher le certificat**.

   ![chemin d’accès du certificat](./media/certificates-for-backend-authentication/rootcert.png)

   Vous devez être en mesure d’afficher les détails du certificat racine.

   ![informations de certificat](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Déplacer vers le **détails** afficher et cliquez sur **copier dans un fichier...**

   ![certificat racine de copie](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. À ce stade, vous avez extrait les détails du certificat racine à partir du certificat de serveur principal. Vous verrez la **Assistant Exportation de certificat**. Présent, utilisez les étapes 2 à 9 mentionné dans la section **certificat d’authentification exportation d’un certificat de serveur principal (pour référence (SKU) de v1)** ci-dessus pour exporter la racine de confiance certificat dans la Base-64 codé X.509 (. Format CER).

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous disposez de l’authentification de certificat/approuvé certificat racine en Base-64 codé X.509 (. Format CER). Vous pouvez ajouter cela à la passerelle d’application à la liste verte vos serveurs principaux pour le chiffrement SSL de bout en bout. Consultez [comment configurer le chiffrement SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).