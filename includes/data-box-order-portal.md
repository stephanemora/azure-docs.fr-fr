---
author: v-dalc
ms.service: databox
ms.subservice: databox
ms.topic: include
ms.date: 09/08/2021
ms.author: alkohli
ms.custom: contperf-fy22q1
ms.openlocfilehash: 6f784c075d24fe26b7ec1ea9d5921dfb460e5298
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743332"
---
Effectuez les étapes suivantes dans le portail Azure pour commander un appareil :

1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter à cette URL : [https://portal.azure.com](https://portal.azure.com).
2. Sélectionnez **+ Créer une ressource**, puis recherchez *Azure Data Box*. Sélectionnez **Azure Data Box**.

   ![Capture d’écran de la section Nouveau avec Azure Data Box dans le champ de recherche](media/data-box-order-portal/data-box-import-01.png)

3. Sélectionnez **Create** (Créer).  

   ![Capture d’écran de la section Azure Data Box avec l’option Créer en évidence](media/data-box-order-portal/data-box-import-02.png)

4. Vérifiez si le service Data Box est disponible dans votre région. Entrez ou sélectionnez les informations suivantes, puis sélectionnez **Appliquer**.

    |Paramètre  |Valeur  |
    |---------|---------|
    |Type de transfert     | Sélectionnez **Importer vers Azure**.        |
    |Abonnement     | Sélectionnez un abonnement EA, CSP ou Azure pour le service Data Box. <br> L’abonnement est lié à votre compte de facturation.       |
    |Resource group | Sélectionnez un groupe de ressources existant. Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble. |
    |Pays/région source    |    Sélectionnez le pays ou la région où vos données se trouvent actuellement.         |
    |Région Azure de destination     |     Sélectionnez la région Azure où vous souhaitez transférer des données. <br> Pour plus d’informations, consultez [Disponibilité de Data Box par région](../articles/databox/data-box-overview.md#region-availability) ou [Disponibilité de Data Box Heavy par région](../articles/databox/data-box-heavy-overview.md#region-availability).  |

    [ ![Démarrage d’une commande d’importation Azure Data Box](media/data-box-order-portal/data-box-import-03.png) ](media/data-box-order-portal/data-box-import-03.png#lightbox)

5. Sélectionnez le produit **Data Box** à commander, c’est-à-dire Data Box, comme indiqué ci-dessous, ou Data Box Heavy. 

    [ ![Capture d’écran montrant la page de sélection d’un produit Azure Data Box. Le bouton Sélectionner de Data Box est mis en évidence.](media/data-box-order-portal/data-box-import-04.png) ](media/data-box-order-portal/data-box-import-04.png#lightbox)

    Pour Data Box, la capacité maximale utilisable pour une commande est de 80 To. Pour Data Box Heavy, la capacité maximale utilisable pour une commande est de 770 To. Vous pouvez créer plusieurs commandes pour des tailles de données supérieures.

    Si vous sélectionnez **Data Box Heavy**, l’équipe Data Box vérifie la disponibilité des appareils dans votre région. Elle vous informera quand vous pourrez poursuivre la commande.

6. Dans **Commande**, accédez à l’onglet **Informations de base**. Entrez ou sélectionnez les informations suivantes. Ensuite, sélectionnez **Suivant : Destination des données>** .

    |Paramètre  |Valeur  |
    |---------|---------|
    |Abonnement      | L’abonnement est automatiquement renseigné en fonction de la sélection antérieure.|
    |Resource group    | Groupe de ressources que vous avez sélectionné précédemment. |
    |Nom de la commande d’importation | Indiquez un nom convivial pour suivre la commande. <ul><li>Le nom peut comporter entre 3 et 24 caractères (lettres, chiffres ou traits d’union).</li><li>Il doit commencer et se terminer par une lettre ou un chiffre.</li></ul>    |

    ![Capture d’écran montrant l’onglet Informations de base pour une commande Data Box avec des exemples d’entrées. L’onglet Informations de base et le bouton « Suivant : Destination des données » sont mis en évidence.](media/data-box-order-portal/data-box-import-05.png)

7. Dans l’écran **Destination des données**, sélectionnez la **Destination des données** : soit les comptes de stockage, soit les disques managés.

    Si vous utilisez un ou plusieurs **comptes de stockage** comme destination de stockage, vous voyez s’afficher l’écran suivant :

    ![Capture d’écran montrant l’onglet Destination des données pour une commande Data Box avec comme destination Compte(s) de stockage. L’onglet Destination des données, l’option Compte(s) de stockage et le bouton Suivant : Sécurité sont mis en évidence.](media/data-box-order-portal/data-box-import-06.png)

    Selon la région Azure spécifiée, sélectionnez un ou plusieurs comptes de stockage dans la liste filtrée des comptes de stockage existants. La Data Box peut être liée à 10 comptes de stockage maximum. Vous pouvez également créer un **compte de stockage blob**, **Usage général v1** ou **Usage général v2**.

   > [!NOTE]
   > - Si vous sélectionnez des comptes Azure Premium FileStorage, le quota provisionné sur le partage de compte de stockage augmente jusqu’à la taille des données copiées dans les partages de fichiers. Une fois le quota augmenté, il n’est pas ajusté de nouveau, par exemple si, pour une raison ou une autre, la Data Box ne peut pas copier vos données.
   > - Ce quota est utilisé pour la facturation. Une fois que vos données sont chargées dans le centre de données, vous devez ajuster le quota pour répondre à vos besoins. Pour plus d’informations, consultez [Présentation de la facturation](../articles/storage/files/understanding-billing.md).

    Les comptes de stockage avec des réseaux virtuels sont pris en charge. Pour autoriser le service Data Box à travailler avec des comptes de stockage sécurisés, activez les services approuvés dans les paramètres de pare-feu réseau du compte de stockage. Pour plus d’informations, découvrez comment [Ajouter Azure Data Box en tant que service approuvé](../articles/storage/common/storage-network-security.md#exceptions).

    Si vous utilisez Data Box pour créer un ou plusieurs **disques managés** à partir de disques durs virtuels (VHD) locaux, vous devez également fournir les informations suivantes :

    |Paramètre  |Valeur  |
    |---------|---------|
    |Groupes de ressources     | Créez un groupe de ressources si vous envisagez de créer des disques managés à partir de disques durs virtuels locaux. Vous pouvez utiliser un groupe de ressources existant uniquement si celui-ci a été créé avant la création d’une commande Data Box de disques managés par le service Data Box. <br> Spécifiez plusieurs groupes de ressources séparés par des points-virgules. Un maximum de 10 groupes de ressources sont pris en charge.|

    ![Capture d’écran montrant l’onglet Destination des données pour une commande Data Box avec comme destination Disques managés. L’onglet Destination des données, l’option Disques managés et le bouton Suivant : Sécurité sont mis en évidence.](media/data-box-order-portal/data-box-import-07.png)

    Le compte de stockage spécifié pour les disques managés est utilisé comme compte de stockage intermédiaire. Le service Data Box charge les disques durs virtuels en tant qu’objets blob de pages sur le compte de stockage intermédiaire avant de convertir celui-ci en disques managés et de le déplacer vers les groupes de ressources. Pour plus d’informations, voir [Vérifier le chargement des données dans Azure](../articles/databox/data-box-deploy-picked-up.md#verify-data-upload-to-azure-8).

   > [!NOTE]
   > Si un objet blob de pages n’est pas correctement converti en disque managé, il reste dans le compte de stockage et des frais de stockage vous sont facturés.

8. Sélectionnez **Suivant : Sécurité>** pour continuer.

    L’écran **Sécurité** vous permet d’utiliser votre propre clé de chiffrement et votre propre appareil, de partager des mots de passe et de choisir d’utiliser le chiffrement double.

    Tous les paramètres dans l’écran **Sécurité** sont facultatifs. Si vous ne changez aucun paramètre, les paramètres par défaut s’appliquent.

    ![Capture d’écran de l’onglet Sécurité concernant une commande d’importation Data Box. L’onglet Sécurité est mis en évidence.](media/data-box-order-portal/data-box-import-08.png)

9. Si vous souhaitez utiliser votre propre clé gérée par le client afin de protéger la clé d’accès de déverrouillage pour votre nouvelle ressource, développez **Type de chiffrement**.

    La configuration d’une clé gérée par le client pour Azure Data Box est facultative. Par défaut, Data Box utilise une clé managée par Microsoft pour protéger la clé d’accès de déverrouillage.

    Une clé gérée par le client n’affecte pas la manière dont les données sont chiffrées sur l’appareil. La clé est utilisée uniquement pour chiffrer la clé d’accès de déverrouillage de l’appareil.

    Si vous ne souhaitez pas utiliser de clé gérée par le client, passez à l’étape 15.

   ![Capture d’écran de l’onglet Sécurité dans l’Assistant Commande Data Box. Les paramètres Type de chiffrement sont développés et mis en évidence.](./media/data-box-order-portal/customer-managed-key-01.png)

10. Pour utiliser une clé gérée par le client, sélectionnez **Clé gérée par le client** comme type de clé. Choisissez ensuite **Sélectionner un coffre de clés et une clé**.
   
    ![Capture d’écran des paramètres Type de chiffrement sous l’onglet Sécurité pour une commande Data Box. Le lien « Sélectionner un coffre de clés et une clé » est mis en évidence.](./media/data-box-order-portal/customer-managed-key-02.png)

11. Dans le panneau **Sélectionner une clé dans Azure Key Vault** :

    - La section **Abonnement** est automatiquement renseignée.

    - Pour **Coffre de clés**, vous pouvez sélectionner un coffre de clés existant dans la liste déroulante.

      ![Capture d’écran des paramètres Type de chiffrement sous l’onglet Sécurité pour une commande Data Box. L’option « Clé gérée par le client » et le lien « Sélectionner un coffre de clés et une clé » sont sélectionnés.](./media/data-box-order-portal/customer-managed-key-03.png)

      Si vous voulez créer un coffre de clés, sélectionnez **Créer un coffre de clés**. 
    
      ![Capture d’écran des paramètres Type de chiffrement sous l’onglet Sécurité pour une commande Data Box. Le lien « Créer un coffre de clés » est mis en évidence.](./media/data-box-order-portal/customer-managed-key-04.png)      

      Ensuite, dans l’écran **Créer un coffre de clés**, entrez le groupe de ressources et un nom de coffre de clés. Assurez-vous que les options **Suppression réversible** et **Protection de purge** sont activées. Acceptez toutes les autres valeurs par défaut, puis sélectionnez **Vérifier + créer**.

      ![Capture d’écran de la page Créer un coffre de clés concernant une commande Data Box. Le groupe de ressources et le nom du coffre de clés sont mis en évidence. Les options Suppression réversible et Protection de purge sont activées.](./media/data-box-order-portal/customer-managed-key-05.png)

      Passez en revue les informations de votre coffre de clés, puis sélectionnez **Créer**. Patientez quelques minutes avant la fin de la création du coffre de clés.

      ![Capture d’écran de l’onglet Vérifier + créer de l’Assistant Création d’un coffre de clés pour Azure. Le bouton Créer est mis en évidence.](./media/data-box-order-portal/customer-managed-key-06.png)

12. Le panneau **Sélectionner une clé** affichera le coffre de clés sélectionné.

    ![Capture d’écran de la page « Sélectionner une clé » dans Azure Key Vault. Le champ Coffre de clés est mis en évidence.](./media/data-box-order-portal/customer-managed-key-07.png)

    Si vous souhaitez créer une nouvelle clé, sélectionnez **Créer une clé**. Vous devez utiliser une clé RSA. La taille peut être supérieure ou égale à 2048. Entrez un nom pour votre nouvelle clé, acceptez les autres valeurs par défaut, puis sélectionnez **Créer**.

      ![Capture d’écran de la page « Créer une clé » dans Azure Key Vault où un nom de clé a été saisi. Le champ Nom et le bouton Créer sont mis en évidence.](./media/data-box-order-portal/customer-managed-key-08.png)

      Vous serez averti quand la clé aura été créée dans votre coffre de clés. Votre nouvelle clé sera sélectionnée et s’affichera dans le panneau **Sélectionner une clé**.

13. Sélectionnez la **Version** de la clé à utiliser, puis choisissez **Sélectionner**.

      ![Capture d’écran de la page « Créer une clé » dans Azure Key Vault. Le champ Version est mis en évidence avec les versions disponibles affichées.](./media/data-box-order-portal/customer-managed-key-09.png)

    Si vous voulez créer une nouvelle version de clé, sélectionnez **Créer une version**.

    ![Capture d’écran de la page « Créer une clé » dans Azure Key Vault. Le lien « Créer une version » est mis en évidence.](./media/data-box-order-portal/customer-managed-key-10.png)

    Choisissez les paramètres de la nouvelle version de clé, puis sélectionnez **Créer**.

    ![Capture d’écran de la boîte de dialogue Créer une clé dans Azure Key Vault avec des exemples de paramètres de champ. Le bouton Créer est mis en évidence.](./media/data-box-order-portal/customer-managed-key-11.png)

    Les paramètres **Type de chiffrement** de l’écran **Sécurité** montrent votre coffre de clés et votre clé.

    ![Capture d’écran de l’onglet Sécurité pour une commande d’importation Data Box. Un coffre de clés et une clé sont mis en évidence dans les paramètres Type de chiffrement.](./media/data-box-order-portal/customer-managed-key-12.png)

14. Sélectionnez une identité d’utilisateur que vous utiliserez pour gérer l’accès à cette ressource. Choisissez **Sélectionner une identité d’utilisateur**. Dans le volet de droite, sélectionnez l’abonnement et l’identité managée à utiliser. Choisissez ensuite **Sélectionner**.

    Une identité managée affectée par l’utilisateur est une ressource Azure autonome qui peut être utilisée pour gérer plusieurs ressources. Pour plus d’informations, consultez [Types d’identités managées](../articles/active-directory/managed-identities-azure-resources/overview.md).  

    Si vous avez besoin de créer une identité managée, suivez les instructions indiquées dans [Créer, lister, supprimer ou affecter un rôle à une identité managée affectée par l’utilisateur à l’aide du portail Azure](../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
    
    ![Capture d’écran de l’onglet Sécurité montrant le panneau « Sélectionner une identité managée affectée par l’utilisateur » dans une commande Data Box. Les champs Abonnement et Identité sélectionnée sont mis en évidence.](./media/data-box-order-portal/customer-managed-key-13.png)

    L’identité d’utilisateur est indiquée dans les paramètres **Type de chiffrement**.

    ![Capture d’écran de l’onglet Sécurité pour une commande d’importation Data Box. L’identité utilisateur sélectionnée est mise en évidence dans les paramètres Type de chiffrement.](./media/data-box-order-portal/customer-managed-key-14.png)

15. Si vous ne souhaitez pas utiliser les mots de passe générés par le système qu’Azure Data Box utilise par défaut, développez **Apporter son propre mot de passe** dans l’écran **Sécurité**.

    Les mots de passe générés par le système sont sécurisés et sont recommandés, à moins que votre organisation en décide autrement.

    ![Capture d’écran de l’option développée « Apporter son propre mot de passe » sous l’onglet Sécurité pour une commande Data Box. L’onglet Sécurité et les options de mot de passe sont mis en évidence.](media/data-box-order-portal/bring-your-own-password-01.png) 

   - Afin d’utiliser votre propre mot de passe pour votre nouvel appareil, dans **Définir la préférence pour le mot de passe de l’appareil**, sélectionnez **Utiliser votre propre mot de passe**, puis tapez un mot de passe conforme aux exigences de sécurité.
     
     Le mot de passe doit être alphanumérique et contenir entre 12 et 15 caractères, au moins une lettre majuscule, une lettre minuscule, un caractère spécial et un chiffre.

     - Caractères spéciaux autorisés : @ # - $ % ^ ! + = ; : _ ( )
     - Caractères non autorisés : I i L o O 0
   
     ![Capture d’écran de l’option « Apporter son propre mot de passe » sous l’onglet Sécurité pour une commande Data Box. Les options Utiliser votre propre mot de passe et Mot de passe de l’appareil sont mises en évidence.](media/data-box-order-portal/bring-your-own-password-02.png)

 - Afin d’utiliser vos propres mots de passe pour les partages :

   1. Dans **Définir la préférence pour les mots de passe de partage**, sélectionnez **Utiliser vos propres mots de passe**, puis **Sélectionnez des mots de passe pour les partages**.
     
       ![Capture d’écran des options d’utilisation de vos mots de passe de partage sous l’onglet Sécurité pour une commande Data Box. Les options Utiliser vos propres mots de passe et Sélectionner des mots de passe pour les partages sont mises en évidence.](media/data-box-order-portal/bring-your-own-password-03.png)

    1. Tapez un mot de passe pour chaque compte de stockage dans la commande. Le mot de passe sera utilisé sur tous les partages pour le compte de stockage.
    
       Le mot de passe doit être alphanumérique et contenir entre 12 et 64 caractères, au moins une lettre majuscule, une lettre minuscule, un caractère spécial et un chiffre.

       - Caractères spéciaux autorisés : @ # - $ % ^ ! + = ; : _ ( )
       - Caractères non autorisés : I i L o O 0
     
    1. Afin d’utiliser le même mot de passe pour tous les comptes de stockage, sélectionnez **Copy to all** (Copier pour tous). 

    1. Lorsque vous avez terminé, sélectionnez **Enregistrer**.
     
       ![Capture d’écran de la page Définir des mots de passe de partage pour une commande Data Box. Le lien Copy To All (Copier pour tous) et le bouton Enregistrer sont mis en évidence.](media/data-box-order-portal/bring-your-own-password-04.png)

    Dans l’écran **Sécurité**, vous pouvez utiliser **Afficher ou modifier les mots de passe** pour changer les mots de passe.

16. Dans **Sécurité**, si vous voulez activer le double chiffrement basé sur logiciel, développez **Double-encryption (for highly secure environments)** Double chiffrement (pour les environnements hautement sécurisés) et sélectionnez **Enable double encryption for the order** (Activer le double chiffrement pour la commande).

    ![Capture d’écran des options de chiffrement double sous l’onglet Sécurité pour une commande Data Box. L’option Activer le chiffrement double pour la commande et le bouton Suivant : Détails du contact sont mis en évidence.](media/data-box-order-portal/double-encryption-01.png)

    Le chiffrement basé sur le logiciel est effectué en plus du chiffrement AES 256 bits des données sur Data Box.

    > [!NOTE]
    > L’activation de cette option peut augmenter le temps nécessaire au traitement des commandes et à la copie des données. Une fois votre commande créée, vous ne pouvez pas changer cette option.

    Sélectionnez **Suivant : Détails du contact>** pour continuer.

17. Dans **Détails du contact**, sélectionnez **+ Ajouter une adresse**.

    ![Capture d’écran de l’onglet Détails du contact pour une commande Data Box. L’onglet Détails du contact et l’option Ajouter une adresse sont mis en évidence.](media/data-box-order-portal/contact-details-01.png)

18. Dans l’écran **Ajouter une adresse**, indiquez vos nom et prénom, le nom et l’adresse postale de la société, ainsi qu’un numéro de téléphone valide. Sélectionnez **Valider l’adresse**. Le service vérifie l’adresse pour connaître la disponibilité du service et vous informe si le service est disponible pour cette adresse.

    ![Capture d’écran de la page Ajouter une adresse pour une commande Data Box. Les options Expédier avec et Ajouter une adresse d’expédition sont mises en évidence.](media/data-box-order-portal/contact-details-02.png)

    Si vous avez sélectionné l’expédition autogérée, vous recevez une notification par e-mail une fois la commande passée. Pour plus d’informations sur l’expédition gérée par vous-même, consultez [Utiliser l’expédition autogérée](../articles/databox/data-box-portal-customer-managed-shipping.md).

19. Sélectionnez **Ajouter une adresse de livraison** une fois que les détails de l’expédition ont été correctement validés. Vous revenez à l’onglet **Détails du contact**.

20. À côté de **E-mail**, entrez une ou plusieurs adresses e-mail. Le service envoie des notifications par courrier électronique concernant les mises à jour de l’état de la commande aux adresses de messagerie spécifiées.

    Nous vous recommandons d’utiliser un e-mail de groupe afin de continuer à recevoir des notifications si un administrateur du groupe quitte l’entreprise.

    ![Capture d’écran montrant la section E-mail de l’onglet Détails du contact pour une commande Data Box. La zone de saisie des adresses e-mail et le bouton Passer en revue + commander sont mis en évidence.](media/data-box-order-portal/contact-details-03.png)

21. Sous **Passer en revue + commander**, passez en revue les informations liées à la commande, au contact, à la notification et aux conditions de confidentialité. Cochez la case correspondant à l’acceptation des conditions de confidentialité.

22. Sélectionnez **Commander**. La création d’une commande peut prendre quelques minutes.

    ![Capture d’écran de l’onglet Passer en revue + commander pour une commande Data Box. L’onglet Passer en revue + commander et le bouton Commander sont mis en évidence.](media/data-box-order-portal/data-box-import-09.png)