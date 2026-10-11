# Guide d'Interconnexion des Sockets Java : Client/Serveur sur 2-machines 

Pour connecter le code de votre **Client** à votre machine **Serveur** depuis un autre ordinateur du même réseau local, vous devez principalement modifier l'adresse IP ciblée par le client et configurer vos machines pour autoriser la connexion.

Voici les **3 étapes indispensables** pour réussir la connexion à distance.

---

## 1. Adapter le code du Client

Sur l'autre machine (le PC client), votre code Java doit pointer directement sur l'adresse IP fixe que vous avez configurée dans le serveur (`192.168.1.10`).

```java
// Dans le projet Java sur la machine CLIENT :
static final String IP_SERVEUR = "192.168.1.10"; // adresse IP de la machine serveur
static final int PORT = 1234;

// Connexion au serveur distant
Socket socket = new Socket(IP_SERVEUR, PORT);

/*
 InetAddress adresseIp = InetAddress.getByName(IP_SERVEUR);
 InetSocketAddress adresseServeur = new InetSocketAddress(IP_SERVEUR, PORT);
 System.out.println("Je suis un client pas encore connecté…");
 Socket socket = new Socket();            // socket créée, pas encore connectée
 socket.connect(adresseServeur, 5000);    // abandon après 5000 ms sans réponse
 System.out.println("je suis un client connecté à " + IP_SERVEUR + ":" + PORT);
*/
```

---

## 2. Configurer le Pare-feu (Firewall) du Serveur

Par défaut, Windows ou macOS bloque les connexions entrantes inconnues sur les ports personnalisés. Si vous n'ouvrez pas le port, le client recevra une erreur du type `Connection refused` ou `Connection timed out`.

### Sur Windows (Serveur)
1. Ouvrez le **Panneau de configuration** > **Système et sécurité** > **Pare-feu Windows Defender**.
2. Cliquez sur **Paramètres avancés** (dans le volet de gauche).
3. Sélectionnez **Règles de trafic entrant**, puis cliquez sur **Nouvelle règle** (dans le volet de droite).
4. Choisissez **Port** > Suivant > **TCP** > Saisissez le port spécifique : `1234`.
5. Sélectionnez **Autoriser la connexion**, validez les profils et donnez un nom à la règle (ex: *Serveur Socket Java*).

### Sur Linux / macOS (Serveur)
Si vous utilisez `ufw` sur Ubuntu/Debian par exemple, ouvrez un terminal et exécutez :
```bash
sudo ufw allow 1234/tcp
```

---

## 3. Vérifier que les machines sont sur le même réseau

Pour que la communication fonctionne, le Serveur et le Client doivent être connectés au **même routeur / même box internet** (soit les deux en Wi-Fi, soit via un câble Ethernet).

### Astuce de test de connectivité
Depuis la machine Client, ouvrez un terminal (Invite de commandes / Terminal) et effectuez un test d'écho sur l'adresse IP du Serveur :
```bash
ping 192.168.1.10
```
* **Si les paquets répondent :** La liaison réseau est établie, le problème ne vient pas du câble ou du Wi-Fi.
* **Si la requête expire ou est inaccessible :** Le pare-feu du serveur bloque toujours la communication ou les deux appareils ne sont pas isolés sur le même sous-réseau.
