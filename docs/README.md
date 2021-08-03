<div style="text-align:center;font-size:30px">﷽</div>









<div style="text-align:center;font-size:48px">Sécuriser l'échange entre un client et un serveur web Apache avec SSL</div>







Travail fait par **CHEBBAH Mehdi** et **HAMMAS Ali Cherif**

---



# Tableau de Contenue

[TOC]

---

#  1. Créer un espace de Publication Web Apache

1.  **Création du répertoire `delta`**

```bash
> sudo mkdir /opt/lampp/htdocs/delta
```

2.  **Modification du fichier `httpd.conf`**

```bash
> sudo nano /opt/lampp/etc/httpd.conf
```

Le fichier `httpd.conf` correspond a la configuration du serveur `http`.

On fait les modifications suivantes:

```bash
#DocumentRoot "/opt/lampp/htdocs" 
DocumentRoot "/opt/lampp/htdocs/delta" 
#<Directory "/opt/lampp/htdocs"> 
<Directory "/opt/lampp/htdocs/delta"> 
```

3.  **Création du fichier `index.html` pour le serveur `http`**

```bash
> sudo echo "Hello World, This is HTTP server." > /opt/lampp/htdocs/delta/index.html
```

4.  #### Redémarrage de Apache

```bash
> sudo /opt/lampp/lampp restart
```

5.  #### Test

```bash
> sudo firefox http://localhost
```

![](Rapport.assets/Capture du 2019-11-19 13:54:10.png)

------



# 2.  Créer un répertoire pour la zone sécurisée

1.  **Création de du répertoire `secure`:**

```bash
> sudo mkdir /opt/lampp/htdocs/delta/secure
```

2.  **Modification du fichier `httpd-ssl.conf`:**

```bash
> sudo nano /opt/lampp/etc/extra/httpd-ssl.conf
```

Le fichier `httpd-ssl.conf` correspond a la configuration de serveur `HTTPS`.

On fait les modifications suivantes:

```bash
#DocumentRoot "/opt/lampp/htdocs" 
DocumentRoot "/opt/lampp/htdocs/delta/secure" 
```

3.  **Création du fichier `index.html` pour le serveur `https`**

```bash
> sudo echo "Hello World, This is a secure HTTP server." > /opt/lampp/htdocs/delta/secure/index.html
```

---------



# 3.  Créer les certificats et les clés pour la CA et le Serveur Web

1.  **Création du certificat du CA:**

On lance tinyca2 par la commande suivante:

```bash
> sudo tinyca2 
```

On remplie les informations de CA comme suit: 

![la config du CA](Rapport.assets/DeepinScreenshot_dde-desktop_20191118203041.png)

On clique sur OK le résultat est:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118204932.png)

2.  **Création des deux répertoires `cles` et `certifs`**

```bash
> sudo mkdir -p /opt/lampp/etc/delta/cles /opt/lampp/etc/delta/certifs
```

3.  **Création du clé et du certificat de serveur**

On clique sur `New` dans l'angle `certificats` puis on choisi `serveur`

On rempli les informations demandés comme suit:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118205714.png)

Puis on sera invité a remplir le mot de pass du CA:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118205741.png)

puis on clique sur `export` dans l'angle `Keys`, on rempli la fenêtre comme suit:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118212316.png)

On confirme qu'on voulez importer la clé sans mot de passe:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118212325.png)

Un message de sucée va être affiche tout suite.

Puis on clique sur l'angle `certificats` puis export et on rempli les informations demandés:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118212646.png)

Un message de sucée va être affiche tout suite.

4.  **Modification du fichier `httpd-ssl.conf`**:

On fait les modifications suivantes:

```bash
#SSLCertificateFile /opt/lampp/etc/ssl.crt/server.crt 
SSLCertificateFile /opt/lampp/etc/delta/certifs/serveurcert.pem 
#SSLCertificateKeyFile /opt/lampp/etc/ssl.key/server.key 
SSLCertificateKeyFile /opt/lampp/etc/delta/cles/serveurkey.pem 
```

-------



# 4.  Les tests

1.  **Redémarrage de `apache`**:

```bash
> sudo /opt/lampp/lampp restart
```

2.  **Test**:

```bash
> sudo firefox https://localhost
```

Firefox va demander si on accepte le certificat du serveur

![firefox demande si accepte le certif](Rapport.assets/Capture du 2019-11-19 13:58:26.png)

![](Rapport.assets/Capture du 2019-11-19 13:58:38.png)

Si on accepte cette certificat la page `index.html` qu'on a inséré a `delta/secure` va être chargé mais avec un avertissement indique que la certificat n'est pas sécurisé.

![la page index.html a le contenu "Hello World, This is a secure HTTP server."](Rapport.assets/Capture du 2019-11-19 14:01:40.png)

![](Rapport.assets/Capture du 2019-11-19 14:02:06.png)

3.  **Exportation du certificat du CA**:

On clique sur l'angle `CA` puis sur `export CA` puis on choisi l'endroit ou on veut enregistrer le certificat du CA

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118215401.png)

On ajoute le certificat au navigateur. Pour faire on suit les étapes suivantes:

![Les image pour ajouter le certificat de CA a firefox](Rapport.assets/Capture du 2019-11-19 14:25:01.png)

![](Rapport.assets/Capture du 2019-11-19 14:26:40.png)

![](Rapport.assets/Capture du 2019-11-19 14:25:41.png)

-------



# 5.  Analyse et comparaison des échanges

### A. Sans authentification du serveur (HTTP)

1.  On remarque que le port utilisé par le serveur est `80` et que le protocole utilisé dans la couche transport pour cette session est `TCP`

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119212406.png)

2.  On remarque que les donnes sont envoyé en clair dans la requête `HTTP` (Pas de confidentialité)

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119212909.png)

3.  On remarque aussi que la repense du serveur n'est pas crypté

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119214041.png)

4.  On remarque aussi qu'il n y a pas d'options dans les paquets  pour vérifier l’identité du serveur ou du client

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119214356.png)

En générale la structure de cette session est la suivante

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119215408.png)

### B. Avec authentification du serveur (HTTPS)

1.  On remarque que le port utilisé par le serveur est `443` et qu'on a utilise deux protocoles dans la couche transport `TCP` et `TLS`

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119215959.png)

2.  On remarque que les données sont cryptées dans les requêtes

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119221319.png)

3.  On remarque aussi que les repenses du serveur sont cryptées

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119221541.png)

4.  On remarque qu'il y a des options dans les paquets `TLS`  pour vérifier l’identité du serveur ou du client

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119222322.png)

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119223012.png)

En générale la structure de cette session est la suivante

![](Rapport.assets/DeepinScreenshot_select-area_20191119225328.png)

Pour le protocole `SSL`

1.  `ClientHello`

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120182418.png)

2.  `ServeurHello`

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120182955.png)

3.  `Certificat` du serveur

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120183208.png)

4.  `ServerkeyExchange`

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120183721.png)

5.  `ServerHelloDone`

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120183854.png)

6.  `Clientkeyexchange`

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120184210.png)

7.  `ChangeSipherSpec` du client

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120184502.png)

8.  `Finished` du client

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120185015.png)

9.  `ChangeSipherSpec` et `Finished` du serveur

    ![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120185212.png)

    -----

    

# 6.  Ajouter un certificat Client

1.  **Création du certificat du client**

On clique sur `New` dans l'angle `certificats` puis on choisi `client`

On rempli les informations demandés comme suit:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118220344.png)

Puis on sera invité a remplir le mot de pass du CA:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118205741.png)

puis on clique sur `export` dans l'angle `Keys` après avoir sélectionner `client`, on rempli la fenêtre comme suit:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118220518.png)

On confirme qu'on voulez importer la clé sans mot de passe:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118220530.png)

Un message de sucée va être affiche tout suite.

Puis on clique sur l'angle `certificats` puis `export` après avoir sélectionner `client` et on rempli les informations demandés:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191118220807.png)

Un message de sucée va être affiche tout suite.

2.  **Modification de apache pour que le serveur exige un certificat pour le client**: 

On ouvre le fichier `httpd-ssl.conf` et on fait le modifications suivantes:

```bash
#SSLCACertificatePath /opt/lampp/etc/ssl.crt 
#SSLCACertificateFile /opt/lampp/etc/ssl.crt/ca-bundle.crt 
SSLCACertificatePath /opt/lampp/etc/delta/certifs/ 
SSLCACertificateFile /opt/lampp/etc/delta/certifs/My_CA-cacert.pem
#SSLVerifyClient require 
#SSLVerifyDepth 10 
SSLVerifyClient require 
SSLVerifyDepth 2 
```

3.  **Testes**

On relance `apache`

```bash
> sudo /opt/lampp/lampp restart
```

On remarque que le client ne peut pas connecter sur le serveur parce que le navigateur ne trouve pas la certificat du client et le serveur demande cette dernier donc le protocole HTTPS génère une alerte au navigateur pour empêcher le client de se connecter au serveur.

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119185952.png)

Si on analyse cette session on utilisant `Wireshark` on trouve

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120190045.png)

4.  **L'ajout de certificat du client dans le navigateur**:

Puisque `Firefox` n'accepte que les certificats des clients qui a l’extension `.p12` on est obligé de re-exporter la certificat dans cette format en suivant les étapes:

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119190903.png)

Puis

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119190918.png)

Ensuit on ajoute la certificat exportée dans le navigateur pour faire :

![](Rapport.assets/DeepinScreenshot_select-area_20191119191211.png)

On clique sur `Import...` puis on choisi le certificat de client crée dans l’étape précédente

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119191604.png)

Une fenêtre demandant le mot de passe va apparaître, on la laisse vide 

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119191621.png)

On remarque qu'une entre va être ajouté a l'angle `Your certificats`

![](Rapport.assets/DeepinScreenshot_select-area_20191119191633.png)

Puis on clique sur `OK`

5.  **Testes**

On relance `apache`

```bash
> sudo /opt/lampp/lampp restart
```

Si on essai d'accéder au serveur via le navigateur on sera invite a choisir une certificats pour être authentifie dans le serveur  

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119205627.png)

Si on clique sur `OK`, alors on peut accéder au serveur avec toutes sécurité

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191119210606.png)

Dans `Wireshark` on trouve

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120190316.png)

On remarque qu'il y a une authentification mutuel entre le serveur et le client

![](Rapport.assets/DeepinScreenshot_dde-desktop_20191120190625.png)

