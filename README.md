
### préparation de l'environnement de travail 

On va utiliser deux VM Linux sur VirtualBox, la premiere est `Kali` et l'autre `Ubuntu Serveur`

> [!NOTE]
> il est possible d'installer l'interface graphique sur Ubuntu serveur pour faciliter les tâches un peu<br>
> ```bash
> sudo apt update && sudo apt upgrade
> ```
> **Installer un gestionnaire d'affichage** <br>
> >  C'est une application qui démarre le serveur d'affichage, lance le bureau et gère l'authentification de l'utilisateur. Le gestionnaire par défaut, GDM3, est gourmand en ressources. Pour économiser les ressources système, envisagez un outil plus léger, tel que SLiM.
> ```
> sudo apt install slim
> ```
> ```
> sudo apt install ubuntu-desktop
> ```
> ```
> sudo reboot
> ```

2- ##Kali(machine A)##

> [!TIP]
> ** installation GPG **
> ```
> sudo apt update && sudo apt upgrade
> ```
> ```
> sudo apt install gnupg
> ```
> ```
> gpg --version
> ```
> ** Installez SSH **
> ```
> sudo apt install openssh-server
> ```
> ```
> sudo systemctl start ssh
> ```
> ** Création d'un Clé **
> Verifier la base de donnée des clé sur cette machine
> ```
> gpg --list-keys
> ```
> Créer un clé
> ```
> gpg --full-generate-key
> ```
> ```
>  `RSA(onlyencrypt)`
>  `1024`
>  `0` (ne s'expire jamais) 
>  Entrez le `nom` , `mail` , `commentaire`
> ```
>  si tout est ok, on rentre le mot de passe
> la clé a été crée
> Verifier si la clé dans la DB
> ```
> gpg --list-keys
> ```
> **Envoyer la clé publique de la machine A->B**
> ```
> gpg --export -a -o public_key.asc <keyID>
> ```
> Le Key ID on le trouve par la command " gpg --list-keys "
> IMAGE
> Envoyer la clé à l'autre machine par SSH 
> scp ./key_id.asc  <ubuntuUserName>@<addresseIp>:/home/<ubuntuUserName>/Desktop/
> nano File1
> cat File1
> gpg -o File1signed -a --sign File1
> gpg --verify <fileName>
> gpg -o <outPutFile> --default-key  <keyiDtoSign> -a --sign <fileToSign>
> gpg -o File1signedE -a -r machineBkey --encrypt File1signed 
> //gpg --encrypt --recipient second_pc_public_key > encrypted_ls_output.gpg
> gpg -o H1signedE -a -recipient machineBkey --encrypt H1signed 
> cat File1signedE
> pwd pour connaitre le chemfin
> scp ./File1signedE  root@ipaddresseKali:/home/root95/Desktop/

2- ## Ubuntu Serveru (Machine B)

> [!TIP]
> //installation GPG
> gpg --import <fichierCle>
> gpg --edit-key [key-id]
> trust
> 5
> (ulitimate)
> save
> gpg --list-keys
> (on remarque que la confiance de cette machine en cette clé est changé à ultimate apres avoir été uknown)
> va recevoir les deux fichiers
> gpg -o File1C --decrypt File1E
> ls
> cat File1C

***

> [!WARNING]
> pour supprimer un clé , il faut d'abbord supprimer la clé privé associé à la clé publique et puis supprimer la clé publique (qui est affiché dans la command gpg --list-keys)
> gpg --delete-secret-keys <ClePubliqueASupprime>
> gpg --list-keys 
> la clé est toujours la, que sa clé privé est supprimé , qui empechait de supprimé la clé>
> gpg --delete-keys <ClePubliqueASupprime>
> maintenant est supprimé
