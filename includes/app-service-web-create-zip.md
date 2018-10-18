## <a name="create-a-project-zip-file"></a>Créer un fichier ZIP de projet

Vérifiez que vous êtes toujours dans le répertoire racine de l’exemple de projet. Créez une archive ZIP contenant tous les éléments de votre projet. La commande suivante utilise l’outil par défaut dans votre terminal :

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Par la suite, vous chargez ce fichier ZIP dans Azure et le déployer dans App Service.