Chiffrement- déchiffrement d'un fichier txt et l'échanger entre deux VM Linux ..



** préparation de l'environnement de travail **

On va utiliser deux VM Linux sur VirtualBox.

on peut installer Desktop sur ubuntu server

> Useful information that users should know, even when skimming content.

> [!NOTE]
> il est possible d'installer l'interface graphique sur Ubuntu serveur pour faciliter les tâches un peu<br>
> ```sudo apt update && sudo apt upgrade```
> ```sudo apt install slim```
> ```sudo apt install ubuntu-desktop```
> ```
> sudo reboot
> ```

 ```
 sudo reboot
 ```



machine A 


//installation GPG
sudo apt update
```
sudo apt install gnupg
```

> gpg --version 


> gpg --list-keys

gpg --full-generate-key

1024

0
(ne s'expire jamais)

entrez le nom , mail , commentaire 

si tout est ok 

O

mnt on rentre le mot de passe

et voila 

gpg --list-keys

> RSA(onlyencrypt)

//Envoyer la clé publique de la machine A->B 
gpg --export -a -o public_key.asc <keyID>

scp ./key_id.asc  root@ipaddresseKali:/home/root95/Desktop/

nano File1

cat File1

gpg -o File1signed -a --sign File1

gpg --verify <fileName>

gpg -o <outPutFile> --default-key  <keyiDtoSign> -a --sign <fileToSign>

gpg -o File1signedE -a -r machineBkey --encrypt File1signed 

---
//gpg --encrypt --recipient second_pc_public_key > encrypted_ls_output.gpg

gpg -o H1signedE -a -recipient machineBkey --encrypt H1signed 

---

cat File1signedE

> pwd pour connaitre le chemfin

scp ./File1signedE  root@ipaddresseKali:/home/root95/Desktop/

 ---
 Machine B 

//installation GPG

gpg --import <fichierCle>

gpg --edit-key [key-id]

trust

5
(ulitimate)
save

gpg --list-keys
(on remarque que la confiance de cette machine en cette clé est changé à ultimate apres avoir été uknown)
va recevoir les deux fichiers

gpg -o File1C --decrypt File1E

ls
cat File1C

***

pour supprimer un clé , il faut d'abbord supprimer la clé privé associé à la clé publique et puis supprimer la clé publique (qui est affiché dans la command gpg --list-keys)

gpg --delete-secret-keys <ClePubliqueASupprime>

gpg --list-keys 
< la clé est toujours la, que sa clé privé est supprimé , qui empechait de supprimé la clé>

gpg --delete-keys <ClePubliqueASupprime>

maintenant est supprimé
