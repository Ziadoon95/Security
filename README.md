
### Préparation de l'environnement de travail

Supposons qu'on a deux VM `Kali` et `Ubuntu Serveur`
 
 <h3 style="color: purple"> Alors sur les deux machines on execute les commandes suivantes</h3>
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
> Configuration de clé :
> > `RSA(onlyencrypt)` , `1024` , `0` (ne s'expire jamais) , `nom` pour une question de lisibilté mettez le nom KaliLinux `mail` et par ex le mail kaliLinux@yahoo.com  `commentaire`
> On rentre le mot de passe, et la clé est crée
>
> 
> **Verifier la DB**
> ```
> gpg --list-keys
> ```
>  ETAPE 2 **Envoyer la clé publique de la machine A->B**
> ```
> gpg --export -a -o key_id.asc <keyID>
> ```
> Example : 
> ```
> gpg --export -a -o key_id.asc 9EFB5AE309097ED783946D7B778D12FF514D11F2
> ```
> la clé publique(key ID) on le trouve par la command " gpg --list-keys " <br>
>  ![what](https://raw.githubusercontent.com/Ziadoon95/Security/main/Screenshot%20(22).png) <br>
> Envoyer la clé à l'autre machine par SSH
> ```
> scp ./key_id.asc  <ubuntuUserName>@<addresseIp>:/home/<ubuntuUserName>/Desktop/
> ```
> Example :
> ```
> scp ./key_id.asc  johnUbuntu@192.168.1.20:/home/johnUbuntu/Desktop/
> ```
> ATTENTION ! ici on est toujour sur Kali (Machine A) pour chiffrer le fichier il faut install GNUPG et SSH et Generer un clé et envoyer La clé publique sur
> Ubuntu (machine B) et l'envoyer à cette machine A


## Sur Ubuntu 
> Sur Desktop ouvre le terminal, verifier si on a reçu la clé publique
> ls
>```
> gpg --import ./key_id.asc
> ```
> Verifier qu'on a reçu la clé
> ```
> gpg --list-keys
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
> le mot Uknown est changé en Ultimate et c'est tres important


## On retourne à Kali
> [!TIP]
> ETAPE 3 **Créer le fichier à chiffrer**
> ```
> nano parKali
> ```
> Ecrivez un text que vous voulez, appuiyez sur ctrl+o, nommez le "parKali" ou c'est que vous voulez
> ```
> cat parKali
> ```
> *CETTE ETAPE EST OPTIONELLE* <br>
> on peut signer un fichier aussi avant de chiffrer...il sert à quoi la signature ? aucune idée :D <br>
> on peut verifier si un fichier a été signé et par quel clé et qui <br>
> Cette commande va signer le fichier avec le mot de passe de notre clé publique, et génére un fichier signé(par -o)
> ```
> gpg -o <fichierGénéréApresLaSignature> -a --sign <fichierASignerEtChiffrer>
> ```
> Example :
> ```
> gpg -o parKaliSigned -a --sign parKali
> ```
> Chiffrer le fichier
> ```
> gpg -o parKaliSignedC -a -r machineBkey --encrypt parKaliSigned
> ```
> Example :
> ```
> gpg -o parKaliSignedC -a -r 9EFB5AE309097ED783946D7B778D12FF514D11F2 --encrypt parKaliSigned 
> ```
> ```
> cat parKaliSignedC
> ```
> Envoyer le fichier Chiffré et signé à l'autre Machine
> ```
> scp ./parKaliSignedC  johnUbuntu@192.168.1.20:/home/johnUbuntu/Desktop/
> ```

 ## Ubuntu Serveru (Machine B)

> [!TIP]
> Verifier si le fichier Chiffré exist sur le bureau de Ubuntu
> ```
> ls
> ```
> ```
> gpg -o parKaliDechiff --decrypt parKaliSignedC
> ```
> ```
> cat parKaliDechiff
> ```
> On peut verifier si ce fichier est signé et par qui :
> ```
> gpg --verify parKaliDechiff
> ```

***


> [!NOTE]
> ```
> Linux va signer le fichier avec la premier clé qu'on a crée, si vous avez crée plusieur clé, on peut lui preciser la clé 
> ```
> ```
> gpg -o <LeFichierQuiSeraGénéréApresLaSignature> --default-key  <laCléQu'onVeutSignerAvec> -a --sign <LeFichieràSigner>
> ```


> [!WARNING]
> pour supprimer un clé , il faut d'abbord supprimer la clé privé associé à la clé publique et puis supprimer la clé publique <br>
> ```
> gpg --delete-secret-keys <ClePubliqueASupprime>
> ```
> ```
> gpg --list-keys
> ```
> la clé publique est toujours la,seulement sa clé privé est supprimé
> ```
> gpg --delete-keys <ClePubliqueASupprime>
> ```
> maintenant est supprimé
