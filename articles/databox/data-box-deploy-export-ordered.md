---
title: Tutoriel sur l’exportation de données à partir d’Azure Data Box | Microsoft Docs
description: Découvrez les prérequis du déploiement et la façon d’exporter les données à partir d’Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.custom: contperf-fy22q1
ms.openlocfilehash: 298d935b9b673e0b77bdd3e66cc3d348e2d52a07
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361521"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>Tutoriel : Créer une commande d’exportation pour Azure Data Box

Azure Data Box est une solution hybride qui vous permet de déplacer des données à partir d’Azure vers votre emplacement. Ce tutoriel explique comment créer une commande d’exportation pour Azure Data Box. La raison principale pour créer une commande d’exportation est la récupération d’urgence, dans le cas où le stockage local est compromis et qu’une sauvegarde doit être restaurée.

Ce tutoriel vous fournira des informations sur :

> [!div class="checklist"]
>
> * Prérequis pour l’exportation
> * Commander un Data Box pour l’exportation
> * Suivre la commande d’exportation
> * Annuler la commande d’exportation

## <a name="prerequisites"></a>Prérequis

Respectez les prérequis de configuration suivants pour l’appareil et le service Data Box avant de commander l’appareil.

### <a name="for-service"></a>Pour le service

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Assurez-vous de disposer d’un groupe de ressources existant que vous pouvez utiliser avec votre Azure Data Box.

* Assurez-vous que votre compte de stockage Azure à partir duquel vous souhaitez exporter les données est l’un des types de compte de stockage pris en charge, comme décrit dans [Comptes de stockage pris en charge pour Data Box](data-box-system-requirements.md#supported-storage-accounts).

### <a name="for-device"></a>Pour l’appareil

Avant de commencer, assurez-vous que :

* Vous disposez d’un ordinateur hôte connecté au réseau du centre de données. Vous allez copier les données à partir d’Azure Data Box vers cet ordinateur. Votre ordinateur hôte doit exécuter un système d’exploitation pris en charge comme décrit dans [Conditions requises pour le système Azure Data Box](data-box-system-requirements.md).

* Votre centre de données doit avoir un réseau haut débit. Nous vous recommandons vivement d’utiliser au minimum une connexion 10 GbE. Si vous ne disposez pas d’une connexion 10-GbE, vous pouvez utiliser une liaison de données 1-GbE, mais les vitesses de copie en seront impactées.

## <a name="order-data-box-for-export"></a>Commander Data Box pour l’exportation

Procédez comme suit dans le portail Azure pour commander un appareil.

1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter à cette URL : [https://portal.azure.com](https://portal.azure.com).

2. Sélectionnez **+ Créer une ressource**, puis recherchez *Azure Data Box*. Sélectionnez **Azure Data Box**.

   ![Créer une ressource](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. Sélectionnez **Create** (Créer).

   ![Créer une ressource Azure Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. Vérifiez si le service Azure Data Box est disponible dans votre région. Entrez ou sélectionnez les informations suivantes, puis sélectionnez **Appliquer**.

    |Paramètre  |Valeur  |
    |---------|---------|
    |Type de transfert     | Sélectionnez **Exporter vers Azure**.        |
    |Abonnement     | Sélectionnez un abonnement EA, CSP ou Azure pour le service Data Box. <br> L’abonnement est lié à votre compte de facturation.       |
    |Resource group     |    Sélectionnez un groupe de ressources existant. <br> Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble.         |
    |Région Azure source    |    Sélectionnez la région Azure où vos données se trouvent actuellement.         |
    |Pays de destination     |     Sélectionnez le pays où vous souhaitez expédier l’appareil.        |

   ![Sélectionner vos paramètres Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. Sélectionnez **Data Box**. La capacité maximale utilisable pour une commande unique est de 80 To. Vous pouvez créer plusieurs commandes pour des tailles de données supérieures.

   ![Sélectionner la capacité Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. Dans **Commande**, indiquez les détails de la commande **De base**. Entrez ou sélectionnez les informations suivantes :

    |Paramètre  |Valeur  |
    |---------|---------|
    |Abonnement     | L’abonnement est automatiquement renseigné en fonction de la sélection antérieure.|
    |Resource group | Groupe de ressources que vous avez sélectionné précédemment. |
    |Nom de la commande d’exportation     |  Indiquez un nom convivial pour suivre la commande. <br> Le nom peut comporter entre 3 et 24 caractères qui peuvent être des lettres, des chiffres et des traits d’union. <br> Il doit commencer et se terminer par une lettre ou un chiffre.      |

    ![Détails de base de la commande d’exportation](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    Sélectionnez **Suivant : Sélection des données** pour continuer.

7. Dans **Sélection des données**, sélectionnez **Ajouter un compte de stockage et un type d’exportation**.

    ![Ajouter un compte de stockage et un type d’exportation](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. Dans **Sélectionner l’option d’exportation**, spécifiez les détails de l’option d’exportation. Entrez ou sélectionnez les informations suivantes, puis sélectionnez **Ajouter**.

    |Paramètre  |Valeur  |
    |---------|---------|
    |Compte de stockage     | Compte de Stockage Azure à partir duquel vous voulez exporter des données. |
    |Type d’exportation     | Spécifie le type de données à exporter à partir de **Tous les objets** et **Utiliser un fichier XML**.<ul><li> **Tous les objets** : spécifie que le travail exporte toutes les données en fonction de votre sélection sous **Options de transfert**.</li><li> **Utiliser un fichier XML** : spécifie un fichier XML qui contient un ensemble de chemins et de préfixes pour les objets Blob et/ou les fichiers à exporter à partir du compte de stockage. Le fichier XML doit se trouver dans le conteneur du compte de stockage sélectionné, et la sélection à partir de partages de fichiers n’est pas prise en charge actuellement. Le fichier doit être un fichier .xml non vide.</li></ul>        |
    |Options de transfert     |  Spécifie les options de transfert de données à partir de **Sélectionner tout**, **Tous les objets Blob** et **Tous les fichiers**. <ul><li> **Sélectionner tout** : spécifie que tous les blobs et fichiers Azure sont exportés. Si vous utilisez un compte de stockage qui prend uniquement en charge les objets Blob (Compte de stockage Blob), l’option **Tous les fichiers** ne peut pas être sélectionnée.</li><li> **Tous les objets Blob** : spécifie que seuls les objets Blob de blocs et de pages sont exportés.</li><li> **Tous les fichiers** : spécifie que tous les fichiers sont exportés à l’exclusion des objets Blob. Le type de compte de stockage que vous avez (GPv1 et GPv2, stockage Premium ou stockage Blob) détermine les types de données que vous pouvez exporter. Pour plus d’informations, consultez [Comptes de stockage pris en charge pour l’exportation](../import-export/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Inclure le journal détaillé     | Indique si vous souhaitez un fichier journal détaillé qui contient une liste de tous les fichiers qui ont bien été exportés. Pour plus d’informations sur les journaux de copie et les journaux détaillés pour une commande d’exportation, consultez [Afficher les journaux](data-box-export-logs.md#view-logs-during-data-copy). |

    > [!NOTE]
    > Si vous sélectionnez **Utiliser un fichier XML** pour le paramètre **Type d’exportation**, vous devez vous assurer que le fichier XML contient des chemins ou des préfixes valides. Vous devez créer et fournir le fichier XML. Si le fichier n’est pas valide ou si aucune donnée ne correspond aux chemins spécifiés, la commande contient des données partielles ou aucune donnée n’est exportée. Pour obtenir de l’aide, consultez [Créer un fichier XML](#create-xml-file).

    Pour savoir comment ajouter un fichier XML à un conteneur, consultez [Commande d’exportation à l’aide d’un fichier XML](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Sélectionner l’option d’exportation](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   Pour voir un exemple de l’entrée XML, consultez [Créer un fichier XML](#create-xml-file).

9. Dans **Sélection des données**, passez en revue vos paramètres, puis sélectionnez **Suivant : Sécurité>** pour continuer.

   ![Commande d’exportation, sélection des données](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    L’écran **Sécurité** vous permet d’utiliser votre propre clé de chiffrement et de choisir également d’utiliser le double chiffrement.

    Tous les paramètres dans l’écran **Sécurité** sont facultatifs. Si vous ne changez aucun paramètre, les paramètres par défaut s’appliquent.

    ![Écran Sécurité de l’Assistant Commande d’importation Data Box](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. Si vous souhaitez utiliser votre propre clé gérée par le client afin de protéger la clé d’accès de déverrouillage pour votre nouvelle ressource, développez **Type de chiffrement**.

    La configuration d’une clé gérée par le client pour Azure Data Box est facultative. Par défaut, Data Box utilise une clé managée par Microsoft pour protéger la clé d’accès de déverrouillage.

    Une clé gérée par le client n’affecte pas la manière dont les données sont chiffrées sur l’appareil. La clé est utilisée uniquement pour chiffrer la clé d'accès de déverrouillage de l’appareil.

    Si vous ne souhaitez pas utiliser de clé gérée par le client, passez à l’étape 16.

    ![Écran Sécurité présentant les paramètres Type de chiffrement](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. Sélectionnez **Clé gérée par le client** comme type de clé. Choisissez ensuite **Sélectionner un coffre de clés et une clé**.
   
    ![Écran Sécurité, paramètres d’une clé gérée par le client](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. Dans l’écran **Sélectionner une clé dans Azure Key Vault**, l’abonnement est automatiquement renseigné.

    - Pour **Coffre de clés**, vous pouvez sélectionner un coffre de clés existant dans la liste déroulante.

      ![Écran Sélectionner une clé dans Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - Vous pouvez également sélectionner **Créer** pour créer un coffre de clés. Dans l’écran **Créer un coffre de clés**, entrez le groupe de ressources et un nom de coffre de clés. Assurez-vous que les options **Suppression réversible** et **Protection de purge** sont activées. Acceptez toutes les autres valeurs par défaut, puis sélectionnez **Vérifier + créer**.

      ![Paramètres Créer un coffre de clés](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      Passez en revue les informations de votre coffre de clés, puis sélectionnez **Créer**. Patientez quelques minutes avant la fin de la création du coffre de clés.

      ![Écran d’examen du nouveau coffre Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. Dans l’écran **Sélectionner une clé dans Azure Key Vault**, vous pouvez sélectionner une clé existante dans le coffre de clés.

    ![Sélectionner une clé existante dans Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    Si vous voulez créer un coffre de clés, sélectionnez **Créer**. Vous devez utiliser une clé RSA. La taille peut être supérieure ou égale à 2048. Entrez un nom pour votre nouvelle clé, acceptez les autres valeurs par défaut, puis sélectionnez **Créer**.

      ![Option permettant de créer une clé](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      Vous serez averti quand la clé aura été créée dans votre coffre de clés.

14. Sélectionnez la **Version** de la clé à utiliser, puis choisissez **Sélectionner**.

      ![Nouvelle clé créée dans le coffre de clés](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    Si vous voulez créer une version de clé, sélectionnez **Créer**.

    ![Ouvrir une boîte de dialogue permettant de créer une version de clé](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    Dans l’écran **Create new key**, choisissez les paramètres de la nouvelle version de clé, puis sélectionnez **Créer**.

    ![Créer une nouvelle version de la clé](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    Les paramètres **Type de chiffrement** de l’écran **Sécurité** montrent votre coffre de clés et votre clé.

    ![Clé et coffre de clés pour une clé gérée par le client](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. Sélectionnez une identité d’utilisateur que vous utiliserez pour gérer l’accès à cette ressource. Choisissez **Sélectionner une identité d’utilisateur**. Dans le volet de droite, sélectionnez l’abonnement et l’identité managée à utiliser. Choisissez ensuite **Sélectionner**.

    Une identité managée affectée par l’utilisateur est une ressource Azure autonome qui peut être utilisée pour gérer plusieurs ressources. Pour plus d’informations, consultez [Types d’identités managées](../active-directory/managed-identities-azure-resources/overview.md).  

    Si vous avez besoin de créer une identité managée, suivez les instructions indiquées dans [Créer, lister, supprimer ou affecter un rôle à une identité managée affectée par l’utilisateur à l’aide du portail Azure](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
    
    ![Sélectionner une identité d’utilisateur](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    L’identité d’utilisateur est indiquée dans les paramètres **Type de chiffrement**.

    Vous pouvez maintenant réduire les paramètres **Type de chiffrement**.

    ![Identité d’utilisateur sélectionnée dans les paramètres Type de chiffrement](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. Si vous voulez activer le double chiffrement basé sur logiciel, développez **Double encryption (for high-security environments) Double chiffrement (pour les environnements hautement sécurisés)** et sélectionnez **Enable double encryption for the order (Activer le double chiffrement pour la commande)** . 

    Le chiffrement basé sur le logiciel est effectué en plus du chiffrement AES 256 bits des données sur Data Box.

    > [!NOTE]
    > L’activation de cette option peut augmenter le temps nécessaire au traitement des commandes et à la copie des données. Une fois votre commande créée, vous ne pouvez pas changer cette option.

    ![Écran Sécurité pour l’importation de Data Box - Double chiffrement](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    Sélectionnez **Suivant : Détails du contact** pour continuer.

11. Dans **Coordonnées**, sélectionnez **+ Ajouter une adresse de livraison** pour entrer vos informations de livraison.

    ![Ajouter une adresse de livraison](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. Dans **Ajouter une adresse de livraison**, indiquez vos nom et prénom, le nom et l’adresse postale de la société, et un numéro de téléphone valide. Sélectionnez **Valider**. Le service valide l’adresse d’expédition de disponibilité du service. Si le service est disponible pour l’adresse de livraison indiquée, vous recevez une notification à cet effet.

    ![Valider l’adresse de livraison](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    Si vous commandez dans une région où l’expédition autogérée est disponible, vous pouvez sélectionner cette option. Pour plus d’informations sur l’expédition gérée par vous-même, consultez [Utiliser l’expédition autogérée](data-box-portal-customer-managed-shipping.md).

13. Sélectionnez **Ajouter une adresse de livraison** une fois que les détails de l’expédition ont été correctement validés.

14. Dans **Coordonnées**, vérifiez votre adresse de livraison et votre adresse e-mail. Le service envoie des notifications par courrier électronique concernant les mises à jour de l’état de la commande aux adresses de messagerie spécifiées.

    Nous vous recommandons d’utiliser un e-mail de groupe afin de continuer à recevoir des notifications si un administrateur du groupe quitte l’entreprise.

    ![Détails du contact](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. Sélectionnez **Suivant : Passer en revue + commander>** . Vous devez accepter les conditions générales pour poursuivre la création de la commande.

16. Sélectionnez **Commander**. La création d’une commande peut prendre quelques minutes.

    ![Validation de la commande](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>Commande d’exportation à l’aide d’un fichier XML

Si vous sélectionnez **Utiliser un fichier XML**, vous pouvez spécifier des conteneurs et des objets Blob spécifiques (page et bloc) à exporter. Les étapes ci-dessous vous montrent comment utiliser le fichier XML pour exporter vos données. Pour créer le fichier XML, suivez les instructions de la section [Créer un fichier XML](#create-xml-file).

Pour utiliser un fichier XML pour exporter vos données :

1. Pour **Type d’exportation**, sélectionnez **Utiliser un fichier XML**. Il s’agit de votre fichier XML qui spécifie les objets Blob et les fichiers Azure que vous souhaitez exporter. Pour ajouter le fichier XML, sélectionnez **Cliquez ici pour sélectionner un fichier XML**.

     ![Sélectionner l’option Exporter, XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. Sélectionnez **+ Conteneur** pour créer un conteneur.

    ![Sélectionner l’option Exporter, Conteneurs](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. Dans l’onglet **Nouveau conteneur** qui s’affiche à partir du côté droit du portail Azure, ajoutez un nom pour le conteneur. Le nom doit être en minuscules et vous pouvez inclure des chiffres et des tirets « - ». Sélectionnez ensuite le **Niveau d’accès public** dans la zone de liste déroulante. Nous vous recommandons de choisir **Privé (accès non anonyme)** pour empêcher d’autres utilisateurs d’accéder à vos données. Pour plus d’informations sur les niveaux d’accès au conteneur, consultez [Autorisations d’accès au conteneur](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

   ![Sélectionner l’option Exporter, paramètres Nouveau conteneur](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. Sélectionnez **Create** (Créer).

   ![Sélectionner l’option Exporter, Créer un conteneur](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   Si votre conteneur est correctement créé, vous recevrez le message suivant :

   ![Le conteneur a été correctement créé](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. Sélectionnez le conteneur que vous avez créé, puis double-cliquez dessus.

   ![Afficher les détails du conteneur](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. Le double-clic sur le conteneur affiche la vue Propriétés du conteneur. Vous souhaitez maintenant joindre (ou accédez à) votre fichier XML qui contient la liste des blobs et/ou des fichiers Azure que vous souhaitez exporter. Sélectionnez **Télécharger**.

   ![Charger un objet blob dans un conteneur](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. Vous avez correctement ajouté le fichier XML au conteneur. Seuls les blobs et les fichiers Azure que vous avez spécifiés dans ce fichier XML seront exportés.

   ![Fichier XML ajouté au conteneur](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="create-xml-file"></a>Créer un fichier XML

Suivez ces instructions pour créer votre fichier XML si vous choisissez de sélectionner les blobs et les fichiers à exporter à l’aide d’un fichier XML :
- **Onglet Exemple de fichier XML :** Copiez un exemple de fichier XML avec des exemples de chaque balise.
- **Onglet Vue d’ensemble du fichier XML :** Examinez les conditions requises relatives aux balises pour le fichier XML.
- **Onglet Exemples de préfixes :** Consultez des exemples de préfixes valides qui sélectionnent plusieurs blobs et fichiers à exporter.

### <a name="sample-xml-file"></a>[Exemple de fichier XML](#tab/sample-xml-file)

Cet exemple de fichier XML contient des exemples de chaque balise XML utilisée pour sélectionner les blobs et les fichiers à exporter dans une commande d’exportation Data Box. 

- Pour connaître la configuration requise pour un fichier XML, accédez à l’onglet **Vue d’ensemble du fichier XML**.
- Pour obtenir plus d’exemples de préfixes valides de blobs et de fichiers, accédez à l’onglet **Exemples de préfixes**.

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!--BlobList selects individual blobs (BlobPath) and multiple blobs (BlobPathPrefix) in Blob storage for export.-->
   <BlobList>
      <BlobPath>/container1/blob.txt</BlobPath> <!-- Exports /container1/blob.txt -->
      <BlobPathPrefix>/container2/</BlobPathPrefix> <!--Exports all blobs in container2 -->
      <BlobPathPrefix>/container</BlobPathPrefix>  <!-- Exports all containers beginning with prefix: "container" -->
      <BlobPathPrefix>/container1/2021Q2</BlobPathPrefix> <!-- Exports all blobs in container1 with prefix: "2021Q2" -->
   </BlobList>
   
   <!--AzureFileList selects individual files (FilePath) and multiple files (FilePathPrefix) in Azure File storage for export.-->
   <AzureFileList>
      <FilePath>/fileshare1/file.txt</FilePath> <!-- Exports /fileshare1/file.txt -->
      <FilePathPrefix>/fileshare1/</FilePath> <!-- Exports all directories and files in fileshare1 -->
      <FilePathPrefix>/fileshare</FilePathPrefix> <!-- Exports all directories and files in any fileshare with prefix: "fileshare" -->
      <FilePathPrefix>/fileshare2/contosowest</FilePathPrefix> <!-- Exports all directories and files in fileshare2 with prefix: "contosowest" -->
   </AzureFileList>
```

### <a name="xml-file-overview"></a>[Vue d’ensemble du fichier XML](#tab/xml-file-overview)

Suivez ces instructions lorsque vous créez le fichier XML pour votre commande d’exportation. Des formats de balises incorrects peuvent entraîner des échecs d’exportation.

Pour connaître les étapes à suivre pour charger le fichier XML lorsque vous passez une commande d’exportation, consultez la section [Commande d’exportation à l’aide d’un fichier XML](#export-order-using-xml-file).

#### <a name="path-vs-prefix"></a>Chemin d’accès ou préfixe

Pour former correctement les balises XML dans votre fichier XML, vous devez comprendre la différence entre un chemin d’accès et un préfixe :

* Un *chemin d’accès* sélectionne et filtre un seul blob ou fichier.
* Un *préfixe* sélectionne et filtre plusieurs blobs ou plusieurs fichiers.

Pour obtenir des exemples de préfixes correctement formés, accédez à l’onglet **Exemples de préfixes**.

#### <a name="tag-usage"></a>Utilisation des balises

Les balises XML suivantes sont utilisées dans le fichier XML pour une commande d’exportation Data Box :

| Balise XML           |Description |
|-------------------|------------|
|`<BlobList>`       |Balise parent pour les balises &lt;BlobPath&gt; et &lt;BlobPathPrefix&gt;.|
|`<BlobPath>`       |Sélectionne un seul blob. |
|`<BlobPathPrefix>` |Sélectionne les blobs ayant un préfixe commun. Pour obtenir des exemples, accédez à l’onglet **Exemples de préfixes**.|
|`<AzureFileList>`  |Balise parent pour les balises &lt;FilePath&gt; et &lt;FilePathPrefix&gt;.|
|`<FilePath>`       |Sélectionne un seul fichier. |
|`<FilePathPrefix>` |Sélectionne les fichiers ayant un préfixe commun. Pour obtenir des exemples, accédez à l’onglet **Exemples de préfixes**.|

Pour voir les balises dans leur contexte, accédez à l’onglet **Exemple de fichier XML**.

#### <a name="xml-tag-requirements"></a>Conditions requises relatives aux balises XML

* Toutes les balises XML respectent la casse et doivent correspondre exactement aux balises du tableau ci-dessus.
* Les balises d’ouverture et de fermeture doivent correspondre.
* Des balises XML ou une mise en forme incorrectes peuvent entraîner l’échec de l’exportation des données.
* Aucune donnée ne sera exportée si le préfixe du blob ou du fichier n’est pas valide. Pour obtenir des exemples de préfixes valides, accédez à l’onglet **Exemples de préfixes**.

### <a name="prefix-examples"></a>[Exemples de préfixes](#tab/prefix-examples)

Ces exemples de chemins d’accès montrent différentes façons de construire un préfixe pour sélectionner plusieurs blobs ou fichiers à exporter.

#### <a name="valid-blob-path-prefixes"></a>Préfixes valides de chemin d’accès de blob

Les exemples de chemins d’accès ci-dessous sont utilisés avec la balise &lt;BlobPathPrefix&gt; pour sélectionner plusieurs blobs dans Stockage Blob Azure à exporter.

|Préfixe de chemin d’accès de blob        |Description                                                                     |Exemple de balise                         |
|------------------------|--------------------------------------------------------------------------------|------------------------------------|
|/                       |Exporte tous les blobs présents dans le compte de stockage.                                       |`<BlobPathPrefix>/</BlobPathPrefix>`|
|/$root/                 |Exporte tous les blobs présents dans le conteneur racine.                                        |`<BlobPathPrefix>/$root/</BlobPathPrefix>`|
|/container2/            |Exporte tous les blobs présents dans le conteneur **container2**.                              |`<BlobPathPrefix>/container2/</BlobPathPrefix>`|
|/container          |Exporte tous les blobs présents dans un conteneur commençant par le préfixe **container**.      |`<BlobPathPrefix>/container</BlobPathPrefix>`|
|/container1/2021Q2      |Exporte tous les blobs présents dans le conteneur **container1** qui commencent par le préfixe **2021Q2**.|`<BlobPathPrefix>/container1/2021Q2</BlobPathPrefix>`|

Pour sélectionner un *seul* blob à exporter, utilisez la balise &lt;BlobPath&gt; avec un chemin d’accès au conteneur et un nom de blob. Par exemple, pour sélectionner **blob.txt** dans le conteneur **container1**, vous devez utiliser cette balise : `<BlobPath>/container1/blob.txt</BlobPath>`.

#### <a name="valid-file-path-prefixes"></a>Préfixes valides de chemin d’accès de fichier

Les exemples de chemins d’accès ci-dessous sont utilisés avec la balise&lt;FilePathPrefix&gt; pour sélectionner plusieurs fichiers Azure à exporter.

|Préfixe de chemin d’accès de fichier        |Description                                                                                          |Exemple de balise|
|------------------------|-----------------------------------------------------------------------------------------------------|-----------|
|/                       |Exporte tous les fichiers et répertoires présents dans le compte de stockage. |`<FilePathPrefix>/</FilePath>Prefix>`|
|/fileshare1/            |Exporte tous les fichiers et répertoires présents dans le partage nommé **fileshare1**.                                                 |`<FilePathPrefix>/fileshare1/</FilePath>Prefix>`|
|/fileshare              |Exporte tous les fichiers et répertoires présents dans un partage de fichiers commençant par le préfixe **fileshare**. |`<FilePathPrefix>/fileshare</FilePath>Prefix>`|
|/fileshare2/contosowest |Exporte tous les fichiers et répertoires présents dans le partage de fichiers **fileshare2** qui commencent par le préfixe **contosowest**.|`<FilePathPrefix>/fileshare2/contosowest</FilePath>Prefix>`|

Pour sélectionner un *seul* fichier à exporter, utilisez la balise &lt;FilePath&gt; avec un chemin d’accès de partage et un nom de fichier. Par exemple, pour sélectionner **file.txt** dans **fileshare1**, vous devez utiliser cette balise : `<FilePath>/fileshare1/file.txt</FilePath>`.

---

## <a name="track-the-order"></a>Suivre la commande

Une fois la commande passée, vous pouvez suivre son état à partir du portail Azure. Accédez à votre commande Data Box, puis à **Vue d’ensemble** pour voir l’état. Le portail affiche la commande avec l’état **Commandé**.

Une fois la préparation de l’appareil terminée, la copie des données démarre à partir des comptes de stockage sélectionnés. Le portail affiche la commande avec l’état **Copie de données en cours**.

![Commande d’exportation Data Box, copie de données en cours](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box copie les données du ou des comptes de stockage source(s). Une fois la copie des données terminée, Data Box est verrouillé et le portail affiche la commande avec l’état **Copie terminée**.

![Commande d’exportation Data Box, copie des données terminée](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

L’exportation de données à partir de stockage Azure vers votre Data Box peut parfois échouer. Vérifiez que les objets blob ne sont pas des objets blob d’archive, car l’exportation de ces objets blob n’est pas prise en charge. 

> [!NOTE]
> Pour les objets blob d’archive, vous devez réalimenter ces objets blob avant qu’ils ne puissent être exportés à partir d’un compte stockage Azure vers votre Data Box. Pour plus d’informations, consultez [Réalimentation d’un objet blob archivé]( ../storage/blobs/storage-blob-rehydration.md).

Si l’appareil n’est pas disponible, vous recevez une notification. Si l’appareil est disponible, Microsoft identifie l’appareil à expédier et le prépare. Pendant la préparation de l’appareil, les actions suivantes se produisent :

* Des partages SMB sont créés pour chaque compte de stockage associé à l’appareil.
* Pour chaque partage, des informations d’identification d’accès (nom d’utilisateur et mot de passe) sont générées.
* L’appareil est verrouillé et est accessible uniquement à l’aide du mot de passe de déverrouillage de l’appareil. Pour récupérer le mot de passe, vous devez vous connecter à votre compte du portail Azure et sélectionner **Détails de l’appareil**.

Ensuite, Microsoft prépare et achemine l’appareil via un transporteur régional. Vous recevez un numéro de suivi une fois l’appareil expédié. Le portail affiche la commande dont l’état est **Distribué**.

![Commande d’exportation Data Box envoyée](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

Si l’expédition autogérée a été sélectionnée, vous recevez une notification par e-mail avec les étapes suivantes lorsque l’appareil est prêt à être récupéré à partir du centre de donnée. Pour plus d’informations sur l’expédition autogérée, consultez [Expédition autogérée](data-box-portal-customer-managed-shipping.md).

![Expédition autogérée prête pour la collecte](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>Annuler la commande

Pour annuler cette commande, dans le portail Azure, accédez à **Vue d’ensemble**, puis sélectionnez **Annuler** dans la barre de commandes.

Après avoir passé la commande, vous pouvez l’annuler à tout moment tant que son traitement n’a pas commencé.

Pour supprimer une commande annulée, accédez à **Vue d’ensemble**, puis sélectionnez **Supprimer** dans la barre de commandes.

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a apporté des connaissances concernant Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
>
> * Prérequis pour l’exportation
> * Commander un Data Box pour l’exportation
> * Suivre la commande d’exportation
> * Annuler la commande d’exportation

Passez au tutoriel suivant pour apprendre à configurer votre Data Box.

> [!div class="nextstepaction"]
> [Configurer votre Azure Data Box](./data-box-deploy-set-up.md)
