
### Préparation de l'environnement de travail

Supposons qu'on a deux VM `Kali` et `Ubuntu Serveur`
> [!IMPORTANT]
> Alors sur les deux machines on execute les commandes suivantes : <br>
> **Installation GPG**
> ```
> sudo apt update && sudo apt upgrade
> ```
> ```
> sudo apt install gnupg
> ```
> Vérifier si GPG est bien installé
> ```
> gpg --version
> ```
> **Installation SSH**
> ```
> sudo apt install openssh-server
> ```
> ```
> sudo systemctl start ssh
> ```
> **Il faut pouvoir faire un PING entre les deux VM et que la service SSH tourne sur les 2 !**


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

## Kali(machine A)

> [!TIP]
> ETAPE 1 **Création d'un Clé** <br>
> Verifier la base de donnée des clé sur cette machine
> ```
> gpg --list-keys
> ```
> Générer un clé
> ```
> gpg --full-generate-key
> ```
> > Configuration de clé :
> > `RSA(onlyencrypt)` `1024` `0` (ne s'expire jamais)  `nom` par une question de lisibilté mettez le nom KaliLinux `mail` et le mail kaliLinux@yahoo.com par example `commentaire`
> > <br>
> On rentre le mot de passe, et la clé est crée  <br>
> Verifier la DB 
> ```
> gpg --list-keys
> ```
>  ETAPE 2 **Envoyer la clé publique de la machine A->B**
> ```
> gpg --export -a -o key_id.asc <keyID>
> ```
> Le Key ID on le trouve par la command " gpg --list-keys " <br>
> IMAGE <br>
> Envoyer la clé à l'autre machine par SSH
> ```
> scp ./key_id.asc  <ubuntuUserName>@<addresseIp>:/home/<ubuntuUserName>/Desktop/
> ```


## Sur Ubuntu 
> Sur Desktop ouvre le terminal, verifier si on a reçu la clé publique
> ls
>```
> gpg --import <./key_id.asc>
> ```
> Modifier la confiance de cette machine à la clé reçu par l'autre machine : IMPORTANT
> ```
> gpg --edit-key <key-id>
> ```
> ```
> `trust`
> `5`
> `(ulitimate)`
> `save`
> ```
> ```
> gpg --list-keys
> ```
> on remarque la le mot Uknown est changé en Ultimate et c'est tres important 


>  ETAPE 3 **Créer le fichier à chiffrer**
> ```
> nano parKali
> ```
> Ecrivez un text que vous voulez, appuiyez sur ctrl+o, nommez le "parKali" ou c'est que vous voulez
> ```
> cat parKali
> ```
> ```
> gpg -o parKaliSigned -a --sign parKali
> ```
> ```
> gpg --verify parKaliSigned
> ```
> ou bien (optionnel)
> ```
> gpg -o <outPutFile> --default-key  <keyiDtoSign> -a --sign <fileToSign>
> ```
> Chiffrer le fichier
> ```
> ATTENTION ! ici on est toujour sur Kali (Machine A) pour chiffrer le fichier il faut install GNUPG et SSH et Generer un clé et envoyer La clé publique sur
> Ubuntu (machine B) et l'envoyer à cette machine A
> gpg -o parKaliSignedC -a -r machineBkey --encrypt parKaliSigned
> ```
> ```
> //gpg --encrypt --recipient second_pc_public_key > encrypted_ls_output.gpg
> ```
> ```
> gpg -o H1signedE -a -recipient machineBkey --encrypt H1signed
> ```
> ```
> cat File1signedE
> ```
> ```
> pwd pour connaitre le chemfin
> ```
> ```
> scp ./File1signedE  root@ipaddresseKali:/home/root95/Desktop/
> ```

 ## Ubuntu Serveru (Machine B)

> [!TIP]
> ```
> gpg --import <fichierCle>
> ```
> ```
> gpg --edit-key [key-id]
> ```
> ```
> trust
> 5
> (ulitimate)
> save
> ```
> ```
> gpg --list-keys
> ```
> (on remarque que la confiance de cette machine en cette clé est changé à ultimate apres avoir été uknown)
> va recevoir les deux fichiers
> ```
> gpg -o File1C --decrypt File1E
> ```
> ```
> ls
> ```
> ```
> cat File1C
> ```

***

> [!WARNING]
> pour supprimer un clé , il faut d'abbord supprimer la clé privé associé à la clé publique et puis supprimer la clé publique (qui est affiché dans la command gpg --list-keys)
> gpg --delete-secret-keys <ClePubliqueASupprime>
> gpg --list-keys 
> la clé est toujours la, que sa clé privé est supprimé , qui empechait de supprimé la clé>
> gpg --delete-keys <ClePubliqueASupprime>
> maintenant est supprimé
